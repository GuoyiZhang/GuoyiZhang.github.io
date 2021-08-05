---
layout:     post
title:      【架构】keepalived：简介、nginx+keepalived集群、nginx+keepalived双主架构
subtitle:   【架构】keepalived：简介、nginx+keepalived集群、nginx+keepalived双主架构
date:       2019-12-09 14:44:40
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 高并发系统设计
    - Nginx
    - 架构学习
---

>【架构】keepalived：简介、nginx+keepalived集群、nginx+keepalived双主架构

# 【架构】keepalived：简介、nginx+keepalived集群、nginx+keepalived双主架构


**目录**

[一、keepalived高可用简介](#%E4%B8%80%E3%80%81keepalived%E9%AB%98%E5%8F%AF%E7%94%A8%E7%AE%80%E4%BB%8B)

[二、nginx+keepalived集群](#%E4%BA%8C%E3%80%81nginx%2Bkeepalived%E9%9B%86%E7%BE%A4)

[1、原理及环境](#1%E3%80%81%E5%8E%9F%E7%90%86%E5%8F%8A%E7%8E%AF%E5%A2%83)

[2、安装配置](#2%E3%80%81%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AE)

[3、测试](#3%E6%B5%8B%E8%AF%95)

[三、nginx+keepalived双主架构](#%E4%B8%89%E3%80%81nginx%2Bkeepalived%E5%8F%8C%E4%B8%BB%E6%9E%B6%E6%9E%84)

[1、原理及环境](#1%E5%8E%9F%E7%90%86%E5%8F%8A%E7%8E%AF%E5%A2%83)

[2、配置文件](#2%E3%80%81%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6)

[3、测试](#3%E6%B5%8B%E8%AF%95-1)

[4、管理与维护](#4%E3%80%81%E7%AE%A1%E7%90%86%E4%B8%8E%E7%BB%B4%E6%8A%A4)

[四、虚拟ip(VIP)申请（VIP(虚拟IP)设置-Ubuntu）](#%E5%9B%9B%E3%80%81%E8%99%9A%E6%8B%9Fip%28VIP%29%E7%94%B3%E8%AF%B7%EF%BC%88VIP%28%E8%99%9A%E6%8B%9FIP%29%E8%AE%BE%E7%BD%AE-Ubuntu%EF%BC%89)

[五、阿里云ecs是否支持搭建nginx+keepalived集群](#%E4%BA%94%E3%80%81%E9%98%BF%E9%87%8C%E4%BA%91ecs%E6%98%AF%E5%90%A6%E6%94%AF%E6%8C%81%E6%90%AD%E5%BB%BAnginx%2Bkeepalived%E9%9B%86%E7%BE%A4)

----

# 一、keepalived高可用简介

keepalived是一个类似与layer3、4和7交换机制的软件，keepalived软件有两种功能，分别是监控检查、VRRP(虚拟路由器冗余协议)
keepalived的作用是检测Web服务器的状态，比如有一台Web服务器、MySQL服务器宕机或工作出现故障，keepalived检测到后，会将故障的Web服务器或者MySQL服务器从系统中剔除，当服务器工作正常后keepalived自动将服务器加入到服务器群中，这些工作全部自动完成，不需要人工干涉，需要人工做的值是修复故障的Web和MySQL服务器。layer3、4、7工作在TCP/IP协议栈的IP层、传输层、应用层，实现原理为：

* layer3：keepalived使用layer3的方式工作时，keepalived会定期向服务器群中的服务器发送一个ICMP数据包，如果发现某台服务的IP地址无法ping通，keepalived便报告这台服务器失效，并将它从服务器集群中剔除。layer3的方式是以服务器的IP地址是否有效作为服务器工作是否正常的标准
* layer4：layer4主要以TCP端口的状态来决定服务器工作是否正常。例如Web服务端口一般为80，如果keepalived检测到80端口没有启动，则keepalived把这台服务器从服务器集群中剔除
* layer7：layer7工作在应用层，keepalived将根据用户的设定检查服务器的运行是否正常，如果与用户的设定不相符，则keepalived将把服务器从服务器集群中剔除

![C.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zMTEuNTFjdG8uY29tLy9pbWFnZXMvMjAxOTAzMDYvMTU1MTgwMzM2NjQxNTQxNS5wbmc?x-oss-process=image/format,png)

![A.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zMTEuNTFjdG8uY29tLy9pbWFnZXMvMjAxOTAzMDYvMTU1MTgwMzM5NzUwMzUwOC5wbmc?x-oss-process=image/format,png)

![B.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zMTEuNTFjdG8uY29tLy9pbWFnZXMvMjAxOTAzMDYvMTU1MTgwMzQxNTQxNTA0Mi5wbmc?x-oss-process=image/format,png)

# 二、nginx+keepalived集群

## 1、原理及环境

Nginx负载均衡一般位于整个架构的最前端或者中间层，如果为最前端时单台nginx会存在单点故障，一台nginx宕机，会影响用户对整个网站的访问。如果需要加入nginx备份服务器，nginx主服务器与备份服务器之间形成高可用，一旦发现nginx主宕机，能够快速将网站切换至备份服务器。

原理图:

![这里写图片描述](https://img-blog.csdn.net/2018081522292985?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xjbF94aWFvd3VndWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

准备环境：
nginx-1：172.25.70.1（master），主机名为：keep1
nginx-2：172.25.70.2（backup），主机名为：keep2

## 2、安装配置

(1)master和backup均安装nginx
/#/#之前预编译需要的gcc、gcc-c++、openssl、openssl-devel等默认已经安装好 [root@keep1 ~]/# yum install -y pcre-devel /#/#安装perl兼容的正则表达式库 [root@keep1 ~]/# cd nginx-1.12.0 [root@keep1 nginx-1.12.0]/# sed -i -e 's/1.12.0//g' -e 's/nginx\//TDTWS/g' -e 's/"NGINX"/"TDTWS"/g' src/core/nginx.h /#/#sed修改Nginx版本信息为TDTWS [root@keep1 nginx-1.12.0]/# ./configure --prefix=/usr/local/nginx --user=www --group=www --with-http_stub_status_module --with-http_ssl_module [root@keep1 nginx-1.12.0]/# make && make install [root@keep1 ~]/# vim /usr/local/nginx/conf/nginx.conf 将该文件里面的user nobody的注释去掉 [root@keep1 ~]/# ln -s /usr/local/nginx/sbin/nginx /sbin/nginx /#创建命令快捷启动 [root@keep1 ~]/# nginx /#没有报错表示启动成功

**(2)master和backup均安装keepalived**

/#/#安装依赖包 [root@keep1 ~]/# yum -y install libnl libnl-devel libnfnetlink 此时还需要一个包libnfnetlink-devel，但因为redhat6.5自身的镜像源中没有，所以给大家提供一个地址，下载了之后直接用rpm -ivh安装即可 [root@localhost ~]/# wget ftp://mirror.switch.ch/mirror/centos/6/os/x86_64/Packages/libnfnetlink-devel-1.0.0-1.el6.x86_64.rpm [root@keep1 keepalived-1.4.3]/# rpm -ivh libnfnetlink-devel-1.0.0-1.el6.x86_64.rpm /#/#编译安装 [root@keep1 ~]/# tar zxf keepalived-1.3.6.tar.gz [root@keep1 ~]/# cd keepalived-1.3.6 [root@keep1 keepalived-1.3.6]/# ./configure --prefix=/usr/local/keepalived --with-init=SYSV [root@keep1 keepalived-1.3.6]/# make && make install /#/#做启动链接等 [root@keep1 keepalived-1.3.6]/# ln -s /usr/local/keepalived/etc/keepalived /etc/ [root@keep1 keepalived-1.3.6]/# ln -s /usr/local/keepalived/etc/sysconfig/keepalived /etc/sysconfig/ [root@keep1 keepalived-1.3.6]/# ln -s /usr/local/keepalived/etc/rc.d/init.d/keepalived /etc/init.d/ [root@keep1 keepalived-1.3.6]/# ln -s /usr/local/keepalived/sbin/keepalived /sbin/ [root@keep1 keepalived-1.3.6]/# chmod +x /usr/local/keepalived/etc/rc.d/init.d/keepalived

**(3)master和backup分别配置keepalived配置文件**

* master
[root@keep1 ~]/# vim /etc/keepalived/keepalived.conf global_defs { notification_email { root@localhost /#健康检查报告通知邮箱 } notification_email_from keepalived@localhost /#发送邮件的地址 smtp_server 127.0.0.1 /#邮件服务器 smtp_connect_timeout 30 route_id LVS_DEVEL } vrrp_script_chk_nginx { script "/data/sh/check_nginx.sh" /#/#检查本地nginx是否存活脚本需要自己写，后面会有该脚本内容 interval 2 weight 2 } /#VIP1 vrrp_instance VI_1 { state BACKUP interface eth0 lvs_sync_daemon_interface eth0 virtual_router_id 151 priority 100 advert_int 5 /#健康检测频率 nopreempt authentication { auth_type PASS auth_pass 1111 } virtual_ipaddress { 172.25.70.100 /#/#VIP } track_script { chk_nginx } } /#/#以下脚本用于检查本地nginx是否存活，如果不存活，则服务实现切换 [root@keep1 ~]/# vim /data/sh/check_nginx.sh /#!/bin/bash killall -0 nginx if [[ $? -ne 0 ]]; then /etc/init.d/keepalived stop fi /#/#编写一个nginx显示的html文件 [root@keep1 ~]/# vim /usr/local/nginx/html/index.html <h1>172.25.70.1</h1> 重新启动nginx

* backup
/#/#backup的keepalived的配置文件和master只有优先级不一样 [root@keep2 ~]/# vim /etc/keepalived/keepalived.conf global_defs { notification_email { root@localhost /#健康检查报告通知邮箱 } notification_email_from keepalived@localhost /#发送邮件的地址 smtp_server 127.0.0.1 /#邮件服务器 smtp_connect_timeout 30 route_id LVS_DEVEL } vrrp_script_chk_nginx { script "/data/sh/check_nginx.sh" /#/#检查本地nginx是否存活脚本需要自己写，后面会有该脚本内容 interval 2 weight 2 } /#VIP1 vrrp_instance VI_1 { state BACKUP interface eth0 lvs_sync_daemon_interface eth0 virtual_router_id 151 priority 90 advert_int 5 /#健康检测频率 nopreempt authentication { auth_type PASS auth_pass 1111 } virtual_ipaddress { 172.25.70.100 /#/#VIP } track_script { chk_nginx } } /#/#backup和master的/data/sh/check_nginx.sh文件相同，这里就不再显示了 /#/#编写一个nginx显示的html文件 [root@keep2 ~]/# vim /usr/local/nginx/html/index.html <h1>172.25.70.2</h1> 重新启动nginx

## 3、测试

**1、两台主机的nginx和keepalived都正常工作，使用浏览器访问虚拟ip 172.25.70.100应该得到keep1主机的nginx页面**

![这里写图片描述](https://img-blog.csdn.net/20180815211805893?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xjbF94aWFvd3VndWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**2、关闭keep1的keepalived，再用浏览器访问虚拟ip查看是否实现了高可用**
如果在真实情况中，主的nginx宕掉了，两个nginx页面一致，那么会快速将网站切换到备份的服务器上面去

![这里写图片描述](https://img-blog.csdn.net/20180815212032520?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xjbF94aWFvd3VndWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

得到上图结果，表示该实验成功！

# 三、nginx+keepalived双主架构

## 1、原理及环境

nginx+keepalived主备模式，始终有一台服务器处于空闲状态。为了更好地利用服务器，可以把它们设置为双主模式，另一台为这一台的备份，同时它又是另外一个VIP的主服务器，两台同时对外提供不同服务，同时接收用户的请求。

原理图：

![这里写图片描述](https://img-blog.csdn.net/20180815224158964?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xjbF94aWFvd3VndWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

环境：

keep1:172.25.70.1
keep2：172.25.70.2
VIP1：172.25.70.100 ，主为keep1，从为keep2
VIP2：172.25.70.150，主为keep2，从为keep1

## 2、配置文件

(1)kepp1主机配置keepalived.conf

其实跟上面的集群都是一个套路，所以这里就没有注释了
 
keep1主机keepalived.conf配置文件内容如下： [root@keep1 ~]/# vim /etc/keepalived/keepalived.conf global_defs { notification_email { root@localhost } notification_email_from keepalived@localhost smtp_server 127.0.0.1 smtp_connect_timeout 30 route_id LVS_DEVEL } vrrp_script_chk_nginx { script "/data/sh/check_nginx.sh" interval 2 weight 2 } /#VIP1 vrrp_instance VI_1 { state MASTER interface eth0 lvs_sync_daemon_interface eth0 virtual_router_id 151 priority 100 advert_int 5 nopreempt authentication { auth_type PASS auth_pass 1111 } virtual_ipaddress { 172.25.70.100 } track_script { chk_nginx } } /#VIP2 vrrp_instance VI_2 { state BACKUP interface eth0 lvs_sync_daemon_interface eth0 virtual_router_id 152 priority 90 advert_int 5 nopreempt authentication { auth_type PASS auth_pass 2222 } virtual_ipaddress { 172.25.70.150 } track_script { chk_nginx } }

**(2)keep2主机配置keepalived.conf**

keep2主机配置keepalived.conf文件内容如下： [root@keep2 ~]/# vim /etc/keepalived/keepalived.conf global_defs { notification_email { root@localhost } notification_email_from keepalived@localhost smtp_server 127.0.0.1 smtp_connect_timeout 30 route_id LVS_DEVEL } vrrp_script_chk_nginx { script "/data/sh/check_nginx.sh" interval 2 weight 2 } /#VIP1 vrrp_instance VI_1 { state BACKUP interface eth0 lvs_sync_daemon_interface eth0 virtual_router_id 151 priority 90 advert_int 5 nopreempt authentication { auth_type PASS auth_pass 1111 } virtual_ipaddress { 172.25.70.100 } track_script { chk_nginx } } /#VIP2 vrrp_instance VI_2 { state MASTER interface eth0 lvs_sync_daemon_interface eth0 virtual_router_id 152 priority 100 advert_int 5 nopreempt authentication { auth_type PASS auth_pass 2222 } virtual_ipaddress { 172.25.70.150 } track_script { chk_nginx } } 配置完成后重新启动服务

**(3)两台服务器上检测脚本还是和集群实验中的脚本内容相同**

## []()3、测试

**1、正常情况下，两个虚拟网卡在它自己为主的那个主机上，如下图**

![这里写图片描述](https://img-blog.csdn.net/20180815230059985?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xjbF94aWFvd3VndWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180815230108140?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xjbF94aWFvd3VndWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**2、当其中一台主服务器DOWN掉，则会发现宕掉的那个VIP的从机开始工作，那么两个VIP此时都会在同一个主机上**
![这里写图片描述](https://img-blog.csdn.net/201808152304482?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xjbF94aWFvd3VndWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

那么双主架构实验则成功！

## 4、管理与维护

**nginx+keepalived双主架构，日常维护和管理需要从以下几个方面：**

* keepalived主配置文件必须设置不同的VRRP名称，同时优先级和VIP也不相同
* 该实验中用到了两台服务器，nginx网站访问量为两台服务器访问之和，可以用脚本来统计
* 两个nginx都是master，有两个VIP地址，用户从外网访问VIP，需要配置域名映射到两个VIP上，可以通过外网DNS映射不同的VIP
* 可以通过zabbix监控等实时监控VIP访问是否正常

# 四、虚拟ip(VIP)申请（VIP(虚拟IP)设置-Ubuntu）

假设有1到5有5台机子，每个机子的ip 分别是 192.168.0.101-192.168.0.105，对外提供服务的时候使用IP为 192.168.0.107，那么只需要对每个机子再设置一个虚IP即可。使用ifconfig 显示目前的IP地址如下：

![这里写图片描述](https://img-blog.csdn.net/20170901150501285?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFyeXdhbmc1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

使用命令设置VIP 如下：
ifconfig eth0:1 192.168.0.107 netmask 255.255.0.0

再查看ip 信息：

![这里写图片描述](https://img-blog.csdn.net/20170901150220345?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFyeXdhbmc1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

用另外一台机子ping 一下 192.168.0.107，再使用 arp -a 查看arp 缓存表，你会发现 107 和105 对同样的MAC地址。

当然还有停掉vip的操作命令：
ifconfig eth0:1 down

如果想重启之后一直生效的话，需要修改网卡配置文件了，不同系统可能不一样，Ubuntu是/etc/network/interfaces文件。

主要参考了两篇文章，如下：
http://www.cnblogs.com/zzPrince/p/5209349.html
http://blog.csdn.net/turkeyzhou/article/details/16971225

# 五、阿里云ecs是否支持搭建nginx+keepalived集群

答案很简单：不支持！可以购买阿里云的slb服务

