---
layout:     post
title:      docker下修改mysql配置文件
subtitle:   docker下修改mysql配置文件
date:       2019-12-24 16:07:28
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Docker
---

>docker下修改mysql配置文件

# docker下修改mysql配置文件


第一步： 找到要修改的镜像
 docker ps
第二步： 进入要修改的镜像
docker exec -it e1066fe2db35 /bin/bash
第三步： 进入要修改的文件目录
cd /etc/mysql
第四步： 安装vim
如果不安装vim在使用vim的时候会报找不到。

apt-get update
apt-get install vim
第五步： 修改my.conf配置文件
第六步： 退出容器
如果要退出bash有2种操作：1）Ctrl + d 退出并停止容器；2）Ctrl + p + q 退出并在后台运行容器；

