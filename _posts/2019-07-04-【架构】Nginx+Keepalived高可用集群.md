---
layout:     post
title:      【架构】Nginx+Keepalived高可用集群
subtitle:   【架构】Nginx+Keepalived高可用集群
date:       2019-07-04 09:12:42
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 架构学习
---

>【架构】Nginx+Keepalived高可用集群

# 【架构】Nginx+Keepalived高可用集群


**1.Keepalived高可用软件**

    Keepalived软件起初是专为LVS负载均衡软件设计的，用来管理并监控LVS集群系统中各个服务节点的状态，后来又加入了可以实现高可用的VRRP功能。因此，keepalived除了能够管理LVS软件外，还可以作为其他服务的高可用解决方案软件。

    keepalived软件主要是通过VRRP协议实现高可用功能的。VRRP是Virtual  Router  Redundancy Protocol（虚拟路由冗余协议）的缩写，VRRP出现的目的就是为了解决静态路由的单点故障问题的，它能保证当个别节点宕机时，整个网络可以不间断地运行。所以，keepalived一方面具有配置管理LVS的功能，同时还具有对LVS下面节点进行健康检查的功能，另一方面也可以实现系统网络服务的高可用功能。

**2.Keepalived高可用故障切换转移原理**

    Keepalived高可用服务对之间的故障切换转移，是通过VRRP来实现的。在keepalived服务工作时，主Master节点会不断地向备节点发送（多播的方式）心跳消息，用来告诉备Backup节点自己还活着。当主节点发生故障时，就无法发送心跳的消息了，备节点也因此无法继续检测到来自主节点的心跳了。于是就会调用自身的接管程序，接管主节点的IP资源和服务。当主节点恢复时，备节点又会释放主节点故障时自身接管的IP资源和服务，恢复到原来的备用角色。

**3. Keepalived高可用实验环境说明**

    如下图所示，前端有两台的Nginx负载均衡器，用来分发接收到客户端的请求。在前文已经配置好了Nginx01，Nginx02也是一样的配置。现在要在两个Nginx负载均衡器上做高可用配置，Nginx01作为主节点，Nginx02作为备节点。

![image.png](https://s1.51cto.com/images/20180407/1523083612988586.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

**4.安装并启用keepalived**

**    **keepalived的安装非常简单，直接使用yum来安装即可。
yum install keepalived -y

    安装之后，启动keepalived服务，顺便把keepalived写入开机启动的脚本里面去。。

/etc/init.d/keepalived star echo "/etc/init.d/keepalived start" >>/etc/rc.local

    启动之后会有三个进程，没问题之后可以关闭keepalived软件，接下来要修改keepalived的配置文件。

![image.png](https://s1.51cto.com/images/20180407/1523068336960962.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

5.修改keepalived配置文件并且重启keepalived服务
/etc/init.d/keepalived stop /#关闭keepalived服务 vim /etc/keepalived/keepalived.conf /#用vim打开编辑
 主节点的配置文件备节点的配置文件! Configuration File for keepalived

 

global_defs {

   notification_email {

     acassen@firewall.loc

     failover@firewall.loc

     sysadmin@firewall.loc

   }

   notification_email_from Alexandre.Cassen@firewall.loc

   smtp_server 192.168.200.1

   smtp_connect_timeout 30

   router_id **lb01**

}

 

vrrp_instance VI_1 {

    state** MASTER**

    interface **eth1**

    virtual_router_id **55**

    priority **150**

    advert_int 1

    authentication {

        auth_type PASS

        auth_pass **123456**

    }

    virtual_ipaddress {

        **192.168.31.5/24 dev eth1 label eth1:1**

    }

}
......
! Configuration File for keepalived

 

global_defs {

   notification_email {

     acassen@firewall.loc

     failover@firewall.loc

     sysadmin@firewall.loc

   }

   notification_email_from Alexandre.Cassen@firewall.loc

   smtp_server 192.168.200.1

   smtp_connect_timeout 30

   router_id **lb02**

}

 

vrrp_instance VI_1 {

    state **BACKUP**

    interface **eth1**

    virtual_router_id **55**

    priority **100**

    advert_int 1

    authentication {

        auth_type PASS

        auth_pass **123456**

    }

    virtual_ipaddress {

        **192.168.31.5 dev eth1 label eth1:1**

    }

}

......

    注解：修改配置文件主要就是上面加粗的几个地方，下面说明一下那几个参数的意思：

    router_id 是路由标识，在一个局域网里面应该是唯一的；vrrp_instance VI_1{...}这是一个VRRP实例，里面定义了keepalived的主备状态、接口、优先级、认证和IP信息；state 定义了VRRP的角色，interface定义使用的接口，这里我的服务器用的网卡都是eth1,根据实际来填写，virtual_router_id是虚拟路由ID标识，一组的keepalived配置中主备都是设置一致，priority是优先级，数字越大，优先级越大，auth_type是认证方式，auth_pass是认证的密码。 virtual_ipaddress ｛...｝定义虚拟IP地址，可以配置多个IP地址，这里我定义为192.168.31.5，绑定了eth1的网络接口，虚拟接口eth1:1.

    修改好主节点之后，保存退出，然后启动keepalived，几分钟内会生成一个虚拟IP：192.168.31.5

![image.png](https://s1.51cto.com/images/20180407/1523085892664166.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

    然后修改备节点的配置文件，保存退出后启动keepalived，不会生成虚拟IP，如果生成那就是配置文件出现了错误。备节点和主节点争用IP资源，这个现象叫做“裂脑”。

![image.png](https://s1.51cto.com/images/20180407/1523086005809963.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

**5.进行高可用的主备服务器切换实验**

    停掉主节点的keepalived服务，查看备节点会不会生成VIP：192.168.31.5

![image.png](https://s1.51cto.com/images/20180407/1523086125505814.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

![image.png](https://s1.51cto.com/images/20180407/1523086152953199.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

    启动主节点的keepalived服务，然后查看主节点和备节点的VIP，主节点应该会抢夺回来VIP：

![image.png](https://s1.51cto.com/images/20180407/1523086230301054.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

![image.png](https://s1.51cto.com/images/20180407/1523086253139598.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

**6.搭配Nginx负载均衡来测试**

    修改windows的hosts文件，把域名指向到VIP上

![image.png](https://s1.51cto.com/images/20180407/1523086603404075.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

    然后用浏览器打开www.pcm.com的页面，在web01上查看access.log日志记录到的客户端IP地址

![image.png](https://s1.51cto.com/images/20180407/1523086691662756.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

![image.png](https://s1.51cto.com/images/20180407/1523086769669020.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

    可以看到日志记录到的客户端的IP地址是192.168.31.1，反向代理服务器是主服务器192.168.31.3.下面我们停止keepalived服务，看备节点会不会接替主节点的VIP和服务。

![image.png](https://s1.51cto.com/images/20180407/1523086887455543.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

![image.png](https://s1.51cto.com/images/20180407/1523086932895336.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

![image.png](https://s1.51cto.com/images/20180407/1523086966983713.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

    可以看到，备节点确实接替了主节点的工作。重新启用主节点，实验的结果就不验证了。

**7.编写Nginx Web服务的守护脚本**

    上面的实验测试有一个问题就是，我们是用Nginx做负载均衡分发请求的数据包的。如果主节点的Keepalived服务正常运行，而Nginx运行异常，那么将会出现Nginx负载均衡服务失灵，无法切换到Nginx负载均衡器02上，后端的Web服务器无法收到请求。所以，我们应该要检测Nginx的服务是否正常运行，如果不是正常运行，应该停掉Keepalived的服务，这样才能自动切换到备节点上。

    我们可以通过检测80端口是否开启来判定Nginx的运行情况，2秒钟检测一次，脚本如下
/#!/bin/bash while true do if [ $(netstat -tlnp|grep nginx|wc -l) -ne 1 ] then /etc/init.d/keepalived stop fi sleep 2 done

    实验的结果可以后台执行命令之后然后停止Nginx服务检验


