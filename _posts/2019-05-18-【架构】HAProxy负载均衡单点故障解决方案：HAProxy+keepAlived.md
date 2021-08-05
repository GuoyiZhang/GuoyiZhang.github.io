---
layout:     post
title:      【架构】HAProxy负载均衡单点故障解决方案：HAProxy+keepAlived
subtitle:   【架构】HAProxy负载均衡单点故障解决方案：HAProxy+keepAlived
date:       2019-05-18 17:14:52
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 架构学习
    - 高并发系统设计
    - HAProxy
    - keepAlived&nbsp;
---
>blog.getTitle() 

# 【架构】HAProxy负载均衡单点故障解决方案：HAProxy+keepAlived


**目录**

[前言](#%E5%89%8D%E8%A8%80)

[介绍](#%E4%BB%8B%E7%BB%8D)

[环境](#%E7%8E%AF%E5%A2%83)

[架构](#%E6%9E%B6%E6%9E%84)

[安装HAProxy](#%E5%AE%89%E8%A3%85HAProxy)

[日志](#%E6%97%A5%E5%BF%97)

[安装KeepAlived](#%E5%AE%89%E8%A3%85KeepAlived)

[高可用测试](#%E9%AB%98%E5%8F%AF%E7%94%A8%E6%B5%8B%E8%AF%95)

[并发测试](#%E5%B9%B6%E5%8F%91%E6%B5%8B%E8%AF%95)

----

# 前言

对于访问量较大的网站来说，随着流量的增加单台服务器已经无法处理所有的请求，这时候需要多台服务器对大量的请求进行分流处理，即负载均衡。而如果实现负载均衡，必须在网站的入口部署服务器（不只是一台）对这些请求进行分发，这台服务器即反向代理。

由于反向代理服务器是网站的入口，其负载压力大且易遭到攻击，**存在单点故障的风险**，所以我们需要一个高可用的方案来实现当一台反向代理服务器宕机的时候，另一台服务器会自动接管服务。基于以上要求，**我们使用HAProxy，KeepAlived来构建高可用的反向代理系统。**

# []()介绍

**[HAProxy](http://haproxy.1wt.eu/)是高性能的代理服务器，其可以提供7层和4层代理，具有healthcheck，负载均衡等多种特性，性能卓越，包括Twitter，Reddit，StackOverflow，GitHub在内的多家知名互联网公司在[使用](http://haproxy.1wt.eu/they-use-it.html)。**

**[KeepAlived](http://www.keepalived.org/)是一个高可用方案，通过VIP(即虚拟IP)和心跳检测来实现高可用。**其原理是存在一组（两台）服务器，分别赋予Master,Backup两个角色，默认情况下Master会绑定VIP到自己的网卡上，对外提供服务。Master,Backup会在一定的时间间隔向对方发送心跳数据包来检测对方的状态，这个时间间隔一般为2秒钟，如果Backup发现Master宕机，那么Backup会发送ARP包到网关，把VIP绑定到自己的网卡，此时Backup对外提供服务，实现自动化的故障转移，当Master恢复的时候会重新接管服务。

# 环境

**OS: CentOS Linux release 6.0 (Final) 2.6.32-71.29.1.el6.x86_64 
HAProxy: 1.4.18 
KeepAlived: 1.2.2 **
VIP: 192.168.1.99 
M: 192.168.1.222 
S: 192.168.1.189

# []()架构

192.168.1.99 +-----------VIP----------+ | | | | Master Backup 192.168.1.189 192.168.1.222 +----------+ +----------+ | HAProxy | | HAProxy | |keepalived| |keepalived| +----------+ +----------+ | v +--------+---------+ | | | | | | v v v +------+ +------+ +------+ | WEB1 | | WEB2 | | WEB3 | +------+ +------+ +------+

## []()安装HAProxy

**安装pcre**
$ yum install pcre $ wget http://haproxy.1wt.eu/download/1.4/src/haproxy-1.4.18.tar.gz $ tar -zxvf haproxy-1.4.18.tar.gz $ cd haproxy-1.4.18

注意编译参数： 

TARGET是指自己系统的内核版本 ARCH指定系统是32位还是64位 
CPU=native: use the build machine's specific processor optimizations 
更多编译参数内容见源码中的README 
$ make TARGET=linux26 ARCH=x86_64 USE_PCRE=1 CPU=native $ make install

配置文件 /etc/haproxy.cfg
global log 127.0.0.1 local3 maxconn 20000 uid 535 /#uid和gid按照实际情况进行配置 gid 520 chroot /var/chroot/haproxy daemon nbproc 1 defaults log 127.0.0.1 local3 mode http option httplog option httpclose option dontlognull option forwardfor retries 2 balance roundrobin stats uri /haproxy-stats contimeout 5000 clitimeout 50000 srvtimeout 50000 frontend http-in bind /*:80 default_backend pool1 backend pool1 option httpchk HEAD / HTTP/1.0 stats refresh 2 server WEB1 192.168.1.189:81 weight 3 maxconn 10000 check server WEB2 192.168.1.222:81 weight 3 maxconn 10000 check

查看HAProxy的状态：http://192.168.1.99/haproxy-stats，这个页面会显示HAProxy本身以及后端服务器的状态。

### 日志

haproxy会把日志记录发送到syslog server(CentOS6下是rsyslogd，UDP514端口)， 编辑/etc/rsyslog.conf文件，添加如下内容：
$ModLoad imudp $UDPServerRun 514 $UDPServerAddress 127.0.0.1 local3./* /var/log/haproxy.log

**重启rsyslog**

$ /etc/init.d/rsyslog restart

自动轮转日志，编辑/etc/logrotate.d/haproxy.cfg，添加如下内容：

/var/log/haproxy.log { rotate 4 daily missingok notifempty compress delaycompress sharedscripts postrotate reload rsyslog > /dev/null 2>&1 || true endscript }

**启动脚本**

$ wget -O haproxy https://raw.github.com/gist/3665034/4125bd5b81977a72e5eec30650fb21f3034782a0/haproxy-init.d $ cp haproxy /etc/init.d/haproxy $ chmod +x /etc/init.d/haproxy /#使用方式 $ /etc/init.d/haproxy start|stop|restart

##  

## []()安装KeepAlived

**安装依赖库**
$ yum install popt popt-devel

**安装KeepAlived**

$ wget http://www.keepalived.org/software/keepalived-1.2.2.tar.gz $ tar -zxvf keepalived-1.2.2.tar.gz $ cd keepalived-1.2.2 $ ./configure --prefix=/usr/local/keepalived $ make && make install $ cp /usr/local/keepalived/etc/rc.d/init.d/keepalived /etc/init.d/keepalived $ cp /usr/local/keepalived/sbin/keepalived /usr/sbin/ $ cp /usr/local/keepalived/etc/sysconfig/keepalived /etc/sysconfig/ $ mkdir -p /etc/keepalived/ $ cp /usr/local/keepalived/etc/keepalived/keepalived.conf /etc/keepalived/keepalived.conf $ chmod +x /etc/init.d/keepalived

**使用方式**

$ /etc/init.d/keepalived start|stop|restart

Master服务器上的配置 /etc/keepalived/keepalived.conf

global_defs { notification_email { user@example.com } notification_email_from mail@example.org smtp_server 192.168.x.x smtp_connect_timeout 30 router_id LVS_DEVEL } /#监测haproxy进程状态，每2秒执行一次 vrrp_script chk_haproxy { script "/usr/local/keepalived/chk_haproxy.sh" interval 2 weight 2 } vrrp_instance VI_1 { state MASTER /#标示状态为MASTER interface eth0 virtual_router_id 51 priority 101 /#MASTER权重要高于BACKUP advert_int 1 mcast_src_ip 192.168.1.189 /#Master服务器IP authentication { auth_type PASS /#主从服务器验证方式 auth_pass 1111 } track_script { chk_haproxy /#监测haproxy进程状态 } /#VIP virtual_ipaddress { 192.168.1.99 /#虚拟IP } }

Bakcup服务器上的配置 /etc/keepalived/keepalived.conf

global_defs { notification_email { user@example.com } notification_email_from mail@example.org smtp_server 192.168.x.x smtp_connect_timeout 30 router_id LVS_DEVEL } /#监测haproxy进程状态，每2秒执行一次 vrrp_script chk_haproxy { script "/usr/local/keepalived/chk_haproxy.sh" interval 2 weight 2 } vrrp_instance VI_1 { state BACKUP /#状态为BACKUP interface eth0 virtual_router_id 51 priority 100 /#权重要低于MASTER advert_int 1 mcast_src_ip 192.168.1.222 /#Backup服务器的IP authentication { auth_type PASS auth_pass 1111 } track_script { chk_haproxy /#监测haproxy进程状态 } /#VIP virtual_ipaddress { 192.168.1.99 /#虚拟IP } }

chk_haproxy.sh内容

/#!/bin/bash /# /# author: weizhifeng /# description: /# 定时查看haproxy是否存在，如果不存在则启动haproxy， /# 如果启动失败，则停止keepalived /# status=$(ps aux|grep haproxy | grep -v grep | grep -v bash | wc -l) if [ "${status}" = "0" ]; then /etc/init.d/haproxy start status2=$(ps aux|grep haproxy | grep -v grep | grep -v bash |wc -l) if [ "${status2}" = "0" ]; then /etc/init.d/keepalived stop fi fi

# 高可用测试

1. 
在Master上停止keepalived，查看系统日志，发现MASTER释放了VIP
$ /etc/init.d/keepalived stop $ tail -f /var/log/message Keepalived: Terminating on signal Keepalived: Stopping Keepalived v1.2.2 (11/03,2011) Keepalived_vrrp: Terminating VRRP child process on signal Keepalived_vrrp: VRRP_Instance(VI_1) removing protocol VIPs.
1. 在Backup上查看系统日志，发现Backup已经进入MASTER角色，并且绑定了VIP 192.168.1.99 
$ tail -f /var/log/message Keepalived_vrrp: VRRP_Instance(VI_1) Entering MASTER STATE Keepalived_vrrp: VRRP_Instance(VI_1) setting protocol VIPs Keepalived_vrrp: VRRP_Instance(VI_1) Sending gratuitous ARPs on eth0 for 192.168.1.99 /#在Backup上查看VIP是否已经绑定
1. 在Master上重新启动keepalived，查看系统日志，发现重新获得MASTER角色，并且绑定VIP 192.168.1.99
 
$ /etc/init.d/keepalived start $ tail -f /var/log/message Keepalived_vrrp: VRRP_Instance(VI_1) Transition to MASTER STATE Keepalived_vrrp: VRRP_Instance(VI_1) Entering MASTER STATE Keepalived_vrrp: VRRP_Instance(VI_1) setting protocol VIPs. Keepalived_vrrp: VRRP_Instance(VI_1) Sending gratuitous ARPs on eth0 for 192.168.1.99
1. 在Backup上查看系统日志，发现其重新回到BACKUP角色，并且释放VIP
 
$ tail -f /var/log/message Keepalived_vrrp: VRRP_Instance(VI_1) Received higher prio advert Keepalived_vrrp: VRRP_Instance(VI_1) Entering BACKUP STATE Keepalived_vrrp: VRRP_Instance(VI_1) removing protocol VIPs.

 

# []()并发测试

我们使用webbench来对HAProxy进行并发测试
$ yum install ctags $ wget http://home.tiscali.cz/~cz210552/distfiles/webbench-1.5.tar.gz $ tar -zxvf webbench-1.5.tar.gz $ cd webbench-1.5 $ make $ mkdir -p /usr/local/man && make install
 
测试环境： 
CPU：Intel 双核 x86_64 主频3191MHZ 
Mem：2G
 
修改php-fpm.conf，设置PHP-FPM spawn的进程数量为100：
 
pm.start_servers = 100 pm.max_spare_servers = 100

测试方法：

$ webbench -c 100 -t 3000 http://192.168.1.99/check.txt $ webbench -c 100 -t 3000 http://192.168.1.99/test.php

测试结果：

并发访问txt文件，HAProxy的session数量为10000左右，这说明HAProxy能够hold住10000个并发连接；并发访问php文件，HAProxy的session峰值为200左右，接近于后端PHP的并发处理能力(100x2)。

