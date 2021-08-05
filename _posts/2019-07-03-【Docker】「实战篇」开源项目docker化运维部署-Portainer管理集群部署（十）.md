# 【Docker】「实战篇」开源项目docker化运维部署-Portainer管理集群部署（十）

之前都是通过命令的方式，管理docker的，其实docker还是有图形界面的。使用图形界面如何管理docker，其实业界很多公司都对docker进行了图形化的封装。之前在初级和中级的时候也有界面marathon。这里说下业界比较出名的portainer。

![](//upload-images.jianshu.io/upload_images/11223715-5ed37fe4be3cb206.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

portainer

* 官网
[https://www.portainer.io](https://www.portainer.io)

![([https://upload-images.jianshu.io/upload_images/11223715-feff5c78729de982.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240](https://upload-images.jianshu.io/upload_images/11223715-feff5c78729de982.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240))

* 介绍
Portainer的开发是为了帮助客户采用Docker容器技术，加快交付价值的时间。构建、管理和维护Docker环境从来没有这么容易。Portainer易于使用为软件开发人员和IT操作提供直观界面的软件。Portainer为您提供了Docker环境的详细概述，并允许您管理容器、图像、网络和卷。Portainer很容易部署——您只需要一个Docker命令就可以在任何地方运行Portainer。

* 为啥现在才说界面管理docker的工具
写了那么多命令，现在才说有一个开源Portainer，其实我的目的就是先学会走，在学会跑。如果直接用图形界面对docker的运行，理解不深入，网络原理也不理解。通过图形界面运行后，可以透过图形界面，理解后台是如何运行命令的。

portainer安装

* 开放Docker网络管理端口（四台机器都需要执行）
vim /lib/systemd/system/docker.service /#找到 ExecStart行 ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock systemctl daemon-reload systemctl restart docker

![](//upload-images.jianshu.io/upload_images/11223715-7af991a88bafc4c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 启动容器（四台机器）
/#66.100机器执行 docker run -d -p 9000:9000 portainer/portainer -H tcp://192.168.66.100:2375 /#66.101机器执行 docker run -d -p 9000:9000 portainer/portainer -H tcp://192.168.66.101:2375 /#66.102机器执行 docker run -d -p 9000:9000 portainer/portainer -H tcp://192.168.66.102:2375 /#66.103机器执行 docker run -d -p 9000:9000 portainer/portainer -H tcp://192.168.66.103:2375

![](//upload-images.jianshu.io/upload_images/11223715-e0dcded1e6623e4d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

 

![](//upload-images.jianshu.io/upload_images/11223715-a3198624edf2c3c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

 

![](//upload-images.jianshu.io/upload_images/11223715-1ac53ca9a4afe9fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

 

![](//upload-images.jianshu.io/upload_images/11223715-de03d4e0584dfa01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 功能页面

![](//upload-images.jianshu.io/upload_images/11223715-531ed034f391f632.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)
可能设置完密码会崩了容器，重新docker start 容器ID

![](//upload-images.jianshu.io/upload_images/11223715-02852702ceb7e53b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-e27869b5154005f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)
如何添加管理的虚拟机

![](//upload-images.jianshu.io/upload_images/11223715-e33b0290d8b7a61c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-f6f3c10e3ba29eca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-44bb5abe23899b7c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-1f8a2476f6e455f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

PS：了解命令的话，其实这个东西很简单的很好用，可能是容器不太稳定或者是我的内存太低，系统老崩，建议崩的话docker start 容器ID 就起起来了。

作者：IT人故事会
链接：https://www.jianshu.com/p/de3a06cf323e
来源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

