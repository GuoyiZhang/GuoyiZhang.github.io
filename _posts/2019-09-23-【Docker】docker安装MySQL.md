---
layout:     post
title:      【Docker】docker安装MySQL
subtitle:   【Docker】docker安装MySQL
date:       2019-09-23 18:50:30
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Docker
---

>【Docker】docker安装MySQL

# 【Docker】docker安装MySQL

GuoyideMacBook-Pro:~ guoyi$ docker pull mysql GuoyideMacBook-Pro:~ guoyi$ docker run --name mysql -v /Users/guoyi/vms/docker/mysql/data:/var/lib/mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -d mysql 7c4bfa17e7d98b57c1630e75226a8386425ab71da996169e7d00eb9e02240562 GuoyideMacBook-Pro:~ guoyi$ docker ps CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES 7c4bfa17e7d9 mysql "docker-entrypoint.s…" 27 seconds ago Up 27 seconds 0.0.0.0:3306->3306/tcp, 33060/tcp mysql GuoyideMacBook-Pro:~ guoyi$ docker ps -a CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES 7c4bfa17e7d9 mysql "docker-entrypoint.s…" 42 seconds ago Up 41 seconds 0.0.0.0:3306->3306/tcp, 33060/tcp mysql 1491f3fa83a6 nginx "nginx -g 'daemon of…" 35 minutes ago Exited (0) 14 minutes ago webserver GuoyideMacBook-Pro:~ guoyi$ docker start 7c4bfa17e7d9 7c4bfa17e7d9 root@localhost 登录即可

 


