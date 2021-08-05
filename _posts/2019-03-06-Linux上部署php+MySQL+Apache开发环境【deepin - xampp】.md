---
layout:     post
title:      Linux上部署php+MySQL+Apache开发环境【deepin - xampp】
subtitle:   Linux上部署php+MySQL+Apache开发环境【deepin - xampp】
date:       2019-03-06 10:28:19
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Linux
    - PHP
    - 开发工具
---
>blog.getTitle() 

# Linux上部署php+MySQL+Apache开发环境【deepin - xampp】


1.下载xampp [Linux](http://lib.csdn.net/base/linux)版本。[www.**apachefriends**.org](https://www.baidu.com/link?url=Kx27eGnYkucJMaunlciYG8p7gwlogQvXDnGYgM9m-SaK1whxqZLV4TZpxNSncDec&wd=&eqid=ece22af800000df10000000357fd9fb0)

2. 准备安装自定义目录中/my/server/lampp

     A. 创建目录 /my/server/lampp

     B. 创建软链接 ln -s /my/server/lampp/ /opt/lampp 

          因为xampp默认安装在opt/lampp目录里，我们这里用讨巧的方式让它安装在我们自定义的目录中

3. 开始安装xampp

    你下载的xampp文件，一般后缀为.run.
/#  chmod +x xampp-linux-x64-5.6.14-0-installer.run /# ./xampp-linux-x64-5.6.14-0-installer.run

4. 安装成功后

启动/# /opt/lampp/lampp start 停止/# /opt/lampp/lampp stop

卸载xampp：

/# /opt/lampp/lampp stop /# rm -rf /opt/lampp

5. 安全设置.如果你不是开发环境，而是部署环境，这个很重要！

/# /opt/lampp/lampp security

* 安全设置，具体的百度下很多

6. 自启动
/#cp /opt/lampp/xampp /etc/rc.d/init.d/xampp   /#chkconfig --add xampp   /#chkconfig xampp on  
