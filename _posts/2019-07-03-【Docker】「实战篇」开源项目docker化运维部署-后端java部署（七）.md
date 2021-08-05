---
layout:     post
title:      【Docker】「实战篇」开源项目docker化运维部署-后端java部署（七）
subtitle:   【Docker】「实战篇」开源项目docker化运维部署-后端java部署（七）
date:       2019-07-03 17:50:04
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 开发工具
    - 「实战篇」开源项目docker化运维部署
---
>blog.getTitle() 

# 【Docker】「实战篇」开源项目docker化运维部署-后端java部署（七）

本节主要说说后端的部署需要注意的点，本身人人快这个项目就是通过springboot来进行开发的，springboot内置的有tomcat的所以，咱们不用在容器内安装tomcat的，直接用罐子文件来进行运行。源码：[https](https://github.com/limingios/netFuture/blob/master/)：[//github.com/limingios/netFuture/blob/master/](https://github.com/limingios/netFuture/blob/master/)后端/后端双机热备
[https://gitee.com/renrenio/renren-fast](https://gitee.com/renrenio/renren-fast)

![](//upload-images.jianshu.io/upload_images/11223715-c0e39321413122bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/556/format/webp)

春天的靴子

* 如何配置Redis的集群
之前配置的Redis的集群，修改下单节点的吧，把所有的Redis的集群都放上去。
 
spring: /# 环境 dev|test|prod profiles: active: dev /# jackson时间格式化 jackson: time-zone: GMT+8 date-format: yyyy-MM-dd HH:mm:ss http: multipart: max-file-size: 100MB max-request-size: 100MB enabled: true redis: open: false /# 是否开启redis缓存 true开启 false关闭 database: 0 /#host: localhost /#port: 6379 /#password: /# 密码（默认为空） timeout: 6000 /# 连接超时时长（毫秒） cluster: nodes: - 172.19.0.2:6379 - 172.19.0.3:6379 - 172.19.0.4:6379 - 172.19.0.5:6379 - 172.19.0.6:6379 - 172.19.0.7:6379 pool: max-active: 1000 /# 连接池最大连接数（使用负值表示没有限制） max-wait: -1 /# 连接池最大阻塞等待时间（使用负值表示没有限制） max-idle: 10 /# 连接池中的最大空闲连接 min-idle: 5 /# 连接池中的最小空闲连接

* Maven的打包工程
renren-fast包含了tomcat.jar文件，准确的来说是springboot的maven，pom中自带的tomcat。所以打包成jar包可以独立运行文件

![](//upload-images.jianshu.io/upload_images/11223715-cb63ff78750de283.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-2b80b5ba7db531dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/760/format/webp)
注意：JAVA后台程序不使用虚拟网络，直接使用宿主的IP端口.--净=主机

* 运行java容器部署后端项目<j1的后台>
docker volume create j1 /#查看j1所在的路径，方便jar包上传 docker volume inspect j1 docker run -it -d name j1 -v j1:/home/soft --net=host java docker exec -it j1 bash /#将编译好的jar拷贝到宿主机上j1所在的目录下 nohubp 就是后台挂机项目 nohup java -jar /home/soft/renren-fast.jar

* 运行java容器部署后端项目<j2的后台>
docker volume create j2 /#查看j2所在的路径，方便jar包上传 docker volume inspect j2 docker run -it -d name j2 -v j2:/home/soft --net=host java docker exec -it j2 bash /#将编译好的jar拷贝到宿主机上j2所在的目录下 nohubp 就是后台挂机项目 nohup java -jar /home/soft/renren-fast.jar

* 运行java容器部署后端项目<j3的后台>
docker volume create j3 /#查看j3所在的路径，方便jar包上传 docker volume inspect j3 docker run -it -d name j3 -v j3:/home/soft --net=host java docker exec -it j3 bash /#将编译好的jar拷贝到宿主机上j3所在的目录下 nohubp 就是后台挂机项目 nohup java -jar /home/soft/renren-fast.jar

设置负载均衡

所有的负载都发送到一个罐包上，如果量比较大，Tomcat的最大承受500的并发，Tomcat的可能就挂了。

![](//upload-images.jianshu.io/upload_images/11223715-8222284d47bb8362.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/828/format/webp)

* nginx的
nginx是性能非常出色的反向代理服务器，最大可以支持8万/秒的并发访问，之前咱们数据库中间件和redis中间件使用了haproxy，因为haproxy对tcp这种负载均衡做的比较好，现在java容器内的tomcat的是支持的HTTP的协议，HTTP负载做的最好的是nginx的，我们选择的nginx负载均衡的产品。

* nginx的的配置<1>
定义了一个上游tomcat内置的都是宿主机器的ip和端口，通过端口的映射找到对应的容器，在服务器中配置好tomcat的和nginx的端口，直接访问nginx，进行跳转到对应的java容器上。端口6101

![](//upload-images.jianshu.io/upload_images/11223715-694be72166db19b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/785/format/webp)

user nginx; worker_processes 1; error_log /var/log/nginx/error.log warn; pid /var/run/nginx.pid; events { worker_connections 1024; } http { include /etc/nginx/mime.types; default_type application/octet-stream; log_format main '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" "$http_x_forwarded_for"'; access_log /var/log/nginx/access.log main; sendfile on; /#tcp_nopush on; keepalive_timeout 65; /#gzip on; proxy_redirect off; proxy_set_header Host $host; proxy_set_header X-Real-IP $remote_addr; proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; client_max_body_size 10m; client_body_buffer_size 128k; proxy_connect_timeout 5s; proxy_send_timeout 5s; proxy_read_timeout 5s; proxy_buffer_size 4k; proxy_buffers 4 32k; proxy_busy_buffers_size 64k; proxy_temp_file_write_size 64k; upstream tomcat { server 192.168.66.100:6001; server 192.168.66.100:6002; server 192.168.66.100:6003; } server { listen 6101; server_name 192.168.66.100; location / { proxy_pass http://tomcat; index index.html index.htm; } } }

* 创建的nginx的指令<1>
nginx的使用宿主的主机IP .--净值=主机
 
/# 容器内的nginx启动加载容器外的配置文件 docker run -it -d --name n1 -v /root/v1/nginx.conf:/etc/nginx/nginx.conf --net=host --privileged nginx

* nginx的的配置<N2>
端口6102
 
user nginx; worker_processes 1; error_log /var/log/nginx/error.log warn; pid /var/run/nginx.pid; events { worker_connections 1024; } http { include /etc/nginx/mime.types; default_type application/octet-stream; log_format main '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" "$http_x_forwarded_for"'; access_log /var/log/nginx/access.log main; sendfile on; /#tcp_nopush on; keepalive_timeout 65; /#gzip on; proxy_redirect off; proxy_set_header Host $host; proxy_set_header X-Real-IP $remote_addr; proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; client_max_body_size 10m; client_body_buffer_size 128k; proxy_connect_timeout 5s; proxy_send_timeout 5s; proxy_read_timeout 5s; proxy_buffer_size 4k; proxy_buffers 4 32k; proxy_busy_buffers_size 64k; proxy_temp_file_write_size 64k; upstream tomcat { server 192.168.66.100:6001; server 192.168.66.100:6002; server 192.168.66.100:6003; } server { listen 6102; server_name 192.168.66.100; location / { proxy_pass http://tomcat; index index.html index.htm; } } }

* 创建的nginx的指令<N2>
nginx的使用宿主的主机IP .--净值=主机
 
/# 容器内的nginx启动加载容器外的配置文件 docker run -it -d --name n2 -v /root/v2/nginx.conf:/etc/nginx/nginx.conf --net=host --privileged nginx

后端项目的双机热备负载均衡方案

之前已经设置了n1和n2，都可以正常的访问后端，但是没有设置keepalived，他们之前无法争抢ip，无法做到双机热备。这次说说双机热备。

![](//upload-images.jianshu.io/upload_images/11223715-e65d39dd8f723cc4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

进入容器N1然后安装KEEPALIVED
 
KEEPALIVED必须在N1所在的容器之内，也可以在泊坞窗仓库里面下载一个nginx的-KEEPALIVED的镜像。这里直接在容器内安装KEEPALIVED。
 
docker exec -it n1 /bin/bash /#写入dns，防止apt-get update找不到服务器 echo "nameserver 8.8.8.8" | tee /etc/resolv.conf > /dev/null apt-get clean apt-get update apt-get install vim vi /etc/apt/sources.list
 
sources.list添加下面的内容
 
deb http://mirrors.163.com/ubuntu/ precise main universe restricted multiverse deb-src http://mirrors.163.com/ubuntu/ precise main universe restricted multiverse deb http://mirrors.163.com/ubuntu/ precise-security universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-security universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-updates universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-proposed universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-proposed universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-backports universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-backports universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-updates universe main multiverse restricted

![](//upload-images.jianshu.io/upload_images/11223715-f4ba54ef59e11ce6?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 更新易源
apt-get clean apt-get update apt-get install keepalived apt-get install vim

![](//upload-images.jianshu.io/upload_images/11223715-cafc951eec9ef527?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* KEEPALIVED配置文件
容器内的路径：/etc/keepalived/keepalived.conf
 
vi /etc/keepalived/keepalived.conf
 
keepalived.conf
 
vrrp_instance VI_1 { state MASTER interface ens33 virtual_router_id 51 priority 100 advert_int 1 authentication { auth_type PASS auth_pass 123456 } virtual_ipaddress { 192.168.66.151 } } virtual_server 192.168.66.151 6201 { delay_loop 3 lb_algo rr lb_kind NAT persistence_timeout 50 protocol TCP real_server 192.168.66.100 6101 { weight 1 } }

1. VI_1名称可以自定义
1. 州MASTER | KEEPALIVED的身份（MASTER主服务器，备份备份服务器，不会抢占虚拟机的IP）。如果都是主MASTER的话，就会进行互相争抢IP，如果抢到了就是MASTER，另一个就是奴隶。
1. 接口网卡，定义一个虚拟IP定义到那个网卡上边。网卡设备的名称.eth33是宿主机是网卡。
1. virtual_router_id 51 | 虚拟路由标识，MASTER和备份的虚拟路由标识必须一致。标识可以是0-255。
1. 优先级100 | 权重.MASTER权重要高于BACKUP数字越大优选级越高。可以根据硬件的配置来完成，权重最大的获取抢到的级别越高。
1. advert_int 1 | 心跳检测的.master与备份节点间同步检查的时间间隔，单位为秒。主备之间必须一致。
1. 身份验证| 主从服务器验证方式。主备必须使用相同的密码才能正常通信。进行心跳检测需要登录到某个主机上边所有有账号密码。
1. virtual_ipaddress | 虚拟ip地址，可以设置多个虚拟ip地址，每行一个。根据上边配置的eth33上配置的ip.192.168.66.151是自己定义的虚拟ip

* 启动keeplived
容器内启动
 
service keepalived start

![](//upload-images.jianshu.io/upload_images/11223715-a67d7009a4cfd0c9?imageMogr2/auto-orient/strip%7CimageView2/2/w/812/format/webp)

图片
进入容器N2然后安装KEEPALIVED
 
KEEPALIVED必须在N2所在的容器之内，也可以在泊坞窗仓库里面下载一个nginx的-KEEPALIVED的镜像。这里直接在容器内安装KEEPALIVED。
 
docker exec -it n2 /bin/bash /#写入dns，防止apt-get update找不到服务器 echo "nameserver 8.8.8.8" | tee /etc/resolv.conf > /dev/null apt-get clean apt-get update apt-get install vim vi /etc/apt/sources.list
 
sources.list添加下面的内容
 
deb http://mirrors.163.com/ubuntu/ precise main universe restricted multiverse deb-src http://mirrors.163.com/ubuntu/ precise main universe restricted multiverse deb http://mirrors.163.com/ubuntu/ precise-security universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-security universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-updates universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-proposed universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-proposed universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-backports universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-backports universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-updates universe main multiverse restricted

![](//upload-images.jianshu.io/upload_images/11223715-f4ba54ef59e11ce6?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 更新易源
apt-get clean apt-get update apt-get install keepalived apt-get install vim

![](//upload-images.jianshu.io/upload_images/11223715-cafc951eec9ef527?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* KEEPALIVED配置文件
容器内的路径：/etc/keepalived/keepalived.conf
 
vi /etc/keepalived/keepalived.conf
 
keepalived.conf
 
vrrp_instance VI_1 { state MASTER interface ens33 virtual_router_id 51 priority 100 advert_int 1 authentication { auth_type PASS auth_pass 123456 } virtual_ipaddress { 192.168.66.151 } } virtual_server 192.168.66.151 6201 { delay_loop 3 lb_algo rr lb_kind NAT persistence_timeout 50 protocol TCP real_server 192.168.66.100 6101 { weight 1 } }

1. VI_1名称可以自定义
1. 州MASTER | KEEPALIVED的身份（MASTER主服务器，备份备份服务器，不会抢占虚拟机的IP）。如果都是主MASTER的话，就会进行互相争抢IP，如果抢到了就是MASTER，另一个就是奴隶。
1. 接口网卡，定义一个虚拟IP定义到那个网卡上边。网卡设备的名称.eth33是宿主机是网卡。
1. virtual_router_id 51 | 虚拟路由标识，MASTER和备份的虚拟路由标识必须一致。标识可以是0-255。
1. 优先级100 | 权重.MASTER权重要高于BACKUP数字越大优选级越高。可以根据硬件的配置来完成，权重最大的获取抢到的级别越高。
1. advert_int 1 | 心跳检测的.master与备份节点间同步检查的时间间隔，单位为秒。主备之间必须一致。
1. 身份验证| 主从服务器验证方式。主备必须使用相同的密码才能正常通信。进行心跳检测需要登录到某个主机上边所有有账号密码。
1. virtual_ipaddress | 虚拟ip地址，可以设置多个虚拟ip地址，每行一个。根据上边配置的eth33上配置的ip.192.168.66.151是自己定义的虚拟ip

* 启动keeplived
容器内启动
 
service keepalived start

![](//upload-images.jianshu.io/upload_images/11223715-a67d7009a4cfd0c9?imageMogr2/auto-orient/strip%7CimageView2/2/w/812/format/webp)

图片

PS：到此未知后端的nginx的双负载，双热备方案已经实现了，

作者：IT人故事会
链接：https://www.jianshu.com/p/25e6a9d8c6ed
来源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

