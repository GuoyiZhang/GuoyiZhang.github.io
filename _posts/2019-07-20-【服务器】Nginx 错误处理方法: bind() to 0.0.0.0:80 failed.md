---
layout:     post
title:      【服务器】Nginx 错误处理方法: bind() to 0.0.0.0:80 failed
subtitle:   【服务器】Nginx 错误处理方法: bind() to 0.0.0.0:80 failed
date:       2019-07-20 12:49:58
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 服务器
---

>【服务器】Nginx 错误处理方法: bind() to 0.0.0.0:80 failed

# 【服务器】Nginx 错误处理方法: bind() to 0.0.0.0:80 failed


今天启动window上的nginx总是报错
错误信息是bind() to 0.0.0.0:80 failed (10013: An attempt was made to access a socket in a way forbidden by its access permissions)

**大概意思是 nginx listen的80后端口被占用   于是百度了下查看端口的命令**

运行–cmd
C:\>netstat -aon|findstr "80"  TCP 127.0.0.1:80 0.0.0.0:0 LISTENING 2448

端口被进程号为2448的进程占用，继续执行下面命令：

C:\>tasklist|findstr "2448" 

svchost.exe 2016 Console 0 16,064 K
很清楚，svchost占用了你的端口,Kill it，svchost是微软的IIS服务器，需要关闭IIS服务器即可！

如果第二步查不到，那就开任务管理器，进程—查看—选择列—pid（进程位标识符）打个勾就可以了
看哪个进程是2448，然后杀之即可。

另外,强制终止进程： CMD命令:taskkill /F /pid 1408

其实上面我都还没解决问题 最后发现有个http.d 这个是apache的进程 结束了这个进程nginx才启动了
 

