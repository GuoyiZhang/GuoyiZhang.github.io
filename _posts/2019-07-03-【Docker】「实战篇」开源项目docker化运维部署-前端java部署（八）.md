---
layout:     post
title:      【Docker】「实战篇」开源项目docker化运维部署-前端java部署（八）
subtitle:   【Docker】「实战篇」开源项目docker化运维部署-前端java部署（八）
date:       2019-07-03 17:52:02
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 开发工具
    - 「实战篇」开源项目docker化运维部署
---
>blog.getTitle() 

# 【Docker】「实战篇」开源项目docker化运维部署-前端java部署（八）

[https://github.com/limingios/netFuture/blob/master/](https://github.com/limingios/netFuture/blob/master/)前端/
[https://github.com/daxiongYang/renren-fast-vue](https://github.com/daxiongYang/renren-fast-vue)

![](//upload-images.jianshu.io/upload_images/11223715-ce286e37619dbeed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

修改连接地址
应该修改成[http://192.168.66.151:6201/renren-fast](http://192.168.66.151:6201/renren-fast);

![](//upload-images.jianshu.io/upload_images/11223715-1db93e0797028ff9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

打包

* 修改镜像，国内打包比较快点
[http://npm.taobao.org/](http://npm.taobao.org/)

![](//upload-images.jianshu.io/upload_images/11223715-7f794447ebfebb40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 安装
你可以使用我们定制的 [cnpm](https://github.com/cnpm/cnpm) (gzip 压缩支持) 命令行工具代替默认的

npm
:
 
$ npm install -g cnpm --registry=https://registry.npm.taobao.org
 
这个目录上传到nginx上。

![](//upload-images.jianshu.io/upload_images/11223715-84e950ff1645aa0e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/462/format/webp)

renren-nginx<1>
这里的nginx并不是做负载均衡的，而是做静态的html的静态运行环境的。

* 创建容器
用宿主机的网段
 
docker run -it -d --name fn1 \ -v /root/fn1/nginx.conf:/etc/nginx/nginxc.conf \ -v /root/fn1/renren-vue:/home/fn1/renren-vue \ --privileged --net=host nginx

* 编写nginx的配置文件
user nginx; worker_processes 1; error_log /var/log/nginx/error.log warn; pid /var/run/nginx.pid; events { worker_connections 1024; } http { include /etc/nginx/mime.types; default_type application/octet-stream; log_format main '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" "$http_x_forwarded_for"'; access_log /var/log/nginx/access.log main; sendfile on; /#tcp_nopush on; keepalive_timeout 65; /#gzip on; proxy_redirect off; proxy_set_header Host $host; proxy_set_header X-Real-IP $remote_addr; proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; client_max_body_size 10m; client_body_buffer_size 128k; proxy_connect_timeout 5s; proxy_send_timeout 5s; proxy_read_timeout 5s; proxy_buffer_size 4k; proxy_buffers 4 32k; proxy_busy_buffers_size 64k; proxy_temp_file_write_size 64k; server { listen 6501; server_name 192.168.66.100; location / { root /home/fn1/renren-vue; index index.html; } } }

renren-nginx<2>

这里的nginx并不是做负载均衡的，而是做静态的html的静态运行环境的。

* 创建容器
用宿主机的网段
 
docker run -it -d --name fn2 \ -v /root/fn2/nginx.conf:/etc/nginx/nginxc.conf \ -v /root/fn2/renren-vue:/home/fn1/renren-vue \ --privileged --net=host nginx

* 编写nginx的配置文件
user nginx; worker_processes 1; error_log /var/log/nginx/error.log warn; pid /var/run/nginx.pid; events { worker_connections 1024; } http { include /etc/nginx/mime.types; default_type application/octet-stream; log_format main '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" "$http_x_forwarded_for"'; access_log /var/log/nginx/access.log main; sendfile on; /#tcp_nopush on; keepalive_timeout 65; /#gzip on; proxy_redirect off; proxy_set_header Host $host; proxy_set_header X-Real-IP $remote_addr; proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; client_max_body_size 10m; client_body_buffer_size 128k; proxy_connect_timeout 5s; proxy_send_timeout 5s; proxy_read_timeout 5s; proxy_buffer_size 4k; proxy_buffers 4 32k; proxy_busy_buffers_size 64k; proxy_temp_file_write_size 64k; server { listen 6502; server_name 192.168.66.100; location / { root /home/fn2/renren-vue; index index.html; } } }

renren-nginx<3>

这里的nginx并不是做负载均衡的，而是做静态的html的静态运行环境的。

* 创建容器
用宿主机的网段
 
docker run -it -d --name fn3 \ -v /root/fn3/nginx.conf:/etc/nginx/nginxc.conf \ -v /root/fn3/renren-vue:/home/fn1/renren-vue \ --privileged --net=host nginx

* 编写nginx的配置文件
user nginx; worker_processes 1; error_log /var/log/nginx/error.log warn; pid /var/run/nginx.pid; events { worker_connections 1024; } http { include /etc/nginx/mime.types; default_type application/octet-stream; log_format main '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" "$http_x_forwarded_for"'; access_log /var/log/nginx/access.log main; sendfile on; /#tcp_nopush on; keepalive_timeout 65; /#gzip on; proxy_redirect off; proxy_set_header Host $host; proxy_set_header X-Real-IP $remote_addr; proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; client_max_body_size 10m; client_body_buffer_size 128k; proxy_connect_timeout 5s; proxy_send_timeout 5s; proxy_read_timeout 5s; proxy_buffer_size 4k; proxy_buffers 4 32k; proxy_busy_buffers_size 64k; proxy_temp_file_write_size 64k; server { listen 6503; server_name 192.168.66.100; location / { root /home/fn3/renren-vue; index index.html; } } }

### qia负载均衡

![](//upload-images.jianshu.io/upload_images/11223715-0521406dd545ab33.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

nginx-ff1

* ff1 容器的创建
docker run -it -d --name ff1 \ -v /root/ff1/nginx.conf:/etc/nginx/nginx.conf \ --net=host \ --privileged --net=host nginx

* 负载均衡ff1 - nginx的配置
user nginx; worker_processes 1; error_log /var/log/nginx/error.log warn; pid /var/run/nginx.pid; events { worker_connections 1024; } http { include /etc/nginx/mime.types; default_type application/octet-stream; log_format main '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" "$http_x_forwarded_for"'; access_log /var/log/nginx/access.log main; sendfile on; /#tcp_nopush on; keepalive_timeout 65; /#gzip on; proxy_redirect off; proxy_set_header Host $host; proxy_set_header X-Real-IP $remote_addr; proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; client_max_body_size 10m; client_body_buffer_size 128k; proxy_connect_timeout 5s; proxy_send_timeout 5s; proxy_read_timeout 5s; proxy_buffer_size 4k; proxy_buffers 4 32k; proxy_busy_buffers_size 64k; proxy_temp_file_write_size 64k; upstream fn { server 192.168.66.100:6501; server 192.168.66.100:6502; server 192.168.66.100:6503; } server { listen 6601; server_name 192.168.66.100; location / { proxy_pass http://fn; index index.html index.htm; } } }

nginx-ff2

* ff2 容器的创建
docker run -it -d --name ff2 \ -v /root/ff2/nginx.conf:/etc/nginx/nginx.conf \ --net=host \ --privileged --net=host nginx

* 负载均衡ff2 - nginx的配置
user nginx; worker_processes 1; error_log /var/log/nginx/error.log warn; pid /var/run/nginx.pid; events { worker_connections 1024; } http { include /etc/nginx/mime.types; default_type application/octet-stream; log_format main '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" "$http_x_forwarded_for"'; access_log /var/log/nginx/access.log main; sendfile on; /#tcp_nopush on; keepalive_timeout 65; /#gzip on; proxy_redirect off; proxy_set_header Host $host; proxy_set_header X-Real-IP $remote_addr; proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; client_max_body_size 10m; client_body_buffer_size 128k; proxy_connect_timeout 5s; proxy_send_timeout 5s; proxy_read_timeout 5s; proxy_buffer_size 4k; proxy_buffers 4 32k; proxy_busy_buffers_size 64k; proxy_temp_file_write_size 64k; upstream fn { server 192.168.66.100:6501; server 192.168.66.100:6502; server 192.168.66.100:6503; } server { listen 6602; server_name 192.168.66.100; location / { proxy_pass http://fn; index index.html index.htm; } } }

前端项目的双机热备负载均衡方案

之前已经设置了ff1 和ff2，都可以正常的访问后端，但是没有设置keepalived，他们之前无法争抢ip，无法做到双机热备。这次说说双机热备。

![](//upload-images.jianshu.io/upload_images/11223715-2f22450c56914920?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

进入容器ff1然后安装keepalived
 
keepalived必须在ff1所在的容器之内，也可以在docker仓库里面下载一个nginx-keepalived的镜像。这里直接在容器内安装keepalived。
 
docker exec -it ff1 /bin/bash /#写入dns，防止apt-get update找不到服务器 echo "nameserver 8.8.8.8" | tee /etc/resolv.conf > /dev/null apt-get clean apt-get update apt-get install vim vi /etc/apt/sources.list
 
sources.list 添加下面的内容
 
deb http://mirrors.163.com/ubuntu/ precise main universe restricted multiverse deb-src http://mirrors.163.com/ubuntu/ precise main universe restricted multiverse deb http://mirrors.163.com/ubuntu/ precise-security universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-security universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-updates universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-proposed universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-proposed universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-backports universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-backports universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-updates universe main multiverse restricted

* 更新apt源
apt-get clean apt-get update apt-get install keepalived apt-get install vim

![](//upload-images.jianshu.io/upload_images/11223715-19bf6ee9786cc44e?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* keepalived配置文件
容器内的路径：/etc/keepalived/keepalived.conf
 
vi /etc/keepalived/keepalived.conf
 
keepalived.conf
 
vrrp_instance VI_1 { state MASTER interface ens33 virtual_router_id 51 priority 100 advert_int 1 authentication { auth_type PASS auth_pass 123456 } virtual_ipaddress { 192.168.66.152 } } virtual_server 192.168.66.152 6701{ delay_loop 3 lb_algo rr lb_kind NAT persistence_timeout 50 protocol TCP real_server 192.168.66.100 6601{ weight 1 } }

1. VI_1 名称可以自定义
1. state MASTER | keepalived的身份（MASTER主服务器，BACKUP备份服务器，不会抢占虚拟机ip）。如果都是主MASTER的话，就会进行互相争抢IP，如果抢到了就是MASTER，另一个就是SLAVE。
1. interface网卡，定义一个虚拟IP定义到那个网卡上边。网卡设备的名称。eth33是宿主机是网卡。
1. virtual_router_id 51 | 虚拟路由标识，MASTER和BACKUP的虚拟路由标识必须一致。标识可以是0-255。
1. priority 100 | 权重。MASTER权重要高于BACKUP 数字越大优选级越高。可以根据硬件的配置来完成，权重最大的获取抢到的级别越高。
1. advert_int 1 | 心跳检测。MASTER与BACKUP节点间同步检查的时间间隔，单位为秒。主备之间必须一致。
1. authentication | 主从服务器验证方式。主备必须使用相同的密码才能正常通信。进行心跳检测需要登录到某个主机上边所有有账号密码。
1. virtual_ipaddress | 虚拟ip地址，可以设置多个虚拟ip地址，每行一个。根据上边配置的eth33上配置的ip。192.168.66.151 是自己定义的虚拟ip

* 启动keeplived
容器内启动
 
service keepalived start
 
进入容器ff2然后安装keepalived
 
keepalived必须在ff2所在的容器之内，也可以在docker仓库里面下载一个nginx-keepalived的镜像。这里直接在容器内安装keepalived。
 
docker exec -it ff2 /bin/bash /#写入dns，防止apt-get update找不到服务器 echo "nameserver 8.8.8.8" | tee /etc/resolv.conf > /dev/null apt-get clean apt-get update apt-get install vim vi /etc/apt/sources.list
 
sources.list 添加下面的内容
 
deb http://mirrors.163.com/ubuntu/ precise main universe restricted multiverse deb-src http://mirrors.163.com/ubuntu/ precise main universe restricted multiverse deb http://mirrors.163.com/ubuntu/ precise-security universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-security universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-updates universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-proposed universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-proposed universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-backports universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-backports universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-updates universe main multiverse restricted

![](//upload-images.jianshu.io/upload_images/11223715-4bf2654b4295a06c?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 更新apt源
apt-get clean apt-get update apt-get install keepalived apt-get install vim

* keepalived配置文件
容器内的路径：/etc/keepalived/keepalived.conf
 
vi /etc/keepalived/keepalived.conf
 
keepalived.conf
 
vrrp_instance VI_1 { state MASTER interface ens33 virtual_router_id 51 priority 100 advert_int 1 authentication { auth_type PASS auth_pass 123456 } virtual_ipaddress { 192.168.66.152 } } virtual_server 192.168.66.152 6701{ delay_loop 3 lb_algo rr lb_kind NAT persistence_timeout 50 protocol TCP real_server 192.168.66.100 6602{ weight 1 } }

1. VI_1 名称可以自定义
1. state MASTER | keepalived的身份（MASTER主服务器，BACKUP备份服务器，不会抢占虚拟机ip）。如果都是主MASTER的话，就会进行互相争抢IP，如果抢到了就是MASTER，另一个就是SLAVE。
1. interface网卡，定义一个虚拟IP定义到那个网卡上边。网卡设备的名称。eth33是宿主机是网卡。
1. virtual_router_id 51 | 虚拟路由标识，MASTER和BACKUP的虚拟路由标识必须一致。标识可以是0-255。
1. priority 100 | 权重。MASTER权重要高于BACKUP 数字越大优选级越高。可以根据硬件的配置来完成，权重最大的获取抢到的级别越高。
1. advert_int 1 | 心跳检测。MASTER与BACKUP节点间同步检查的时间间隔，单位为秒。主备之间必须一致。
1. authentication | 主从服务器验证方式。主备必须使用相同的密码才能正常通信。进行心跳检测需要登录到某个主机上边所有有账号密码。
1. virtual_ipaddress | 虚拟ip地址，可以设置多个虚拟ip地址，每行一个。根据上边配置的eth33上配置的ip。192.168.66.151 是自己定义的虚拟ip

* 启动keeplived
容器内启动
 
service keepalived start

PS：前后端部署基本是一样的都是按照思路，先启动多个容器，然后建立2个负载，负载内安装keepalived做热备。重点是想好端口。但是说实话，这是平常练习和个人项目，如果是多台机器，就不能这么搞了，下次一起通过docker swarm的网络方式让多台机器。

作者：IT人故事会
链接：https://www.jianshu.com/p/cd8098ed1b16
来源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

