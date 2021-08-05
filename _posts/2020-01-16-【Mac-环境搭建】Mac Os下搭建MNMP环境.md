---
layout:     post
title:      【Mac-环境搭建】Mac Os下搭建MNMP环境
subtitle:   【Mac-环境搭建】Mac Os下搭建MNMP环境
date:       2020-01-16 18:05:52
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 服务器
    - Mac
    - 开发工具
---

>【Mac-环境搭建】Mac Os下搭建MNMP环境

# 【Mac-环境搭建】Mac Os下搭建MNMP环境


**MNMP**是指Mac+Nginx+MySQL+PHP。
Mac在这里是指Mac系统。
Nginx是一个高性能的HTTP和反向代理服务器。
MySQL是一个小型关系型数据库管理系统。
PHP是一种在服务器端执行的嵌入HTML文档的脚本语言。

----

在Mac下需要一个Homebrew，类似于之前用unbuntu下的apt-get，通过brew命令，快速安装软件包。
Homebrew网址
[https://brew.sh/index_zh-cn.html](https://link.jianshu.com?t=https%3A%2F%2Fbrew.sh%2Findex_zh-cn.html)
 
通过以下命令 /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 进行安装

----

**安装Nginx**
brew install nginx

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8yMDY0NDA0LTdkYjQ0MzYyM2ZlN2M1NjcucG5nP2ltYWdlTW9ncjIvYXV0by1vcmllbnQvc3RyaXB8aW1hZ2VWaWV3Mi8yL3cvMTIwMC9mb3JtYXQvd2VicA?x-oss-process=image/format,png)

WX20171213-184418@2x.png
sudo nginx //启动nginx服务 默认127.0.0.1:8080端口 sudo nginx -s reload|reopen|quit //重新加载|重启|退出

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8yMDY0NDA0LTFkZmMwNzJmYTQ4NTExMDkucG5nP2ltYWdlTW9ncjIvYXV0by1vcmllbnQvc3RyaXB8aW1hZ2VWaWV3Mi8yL3cvMTIwMC9mb3JtYXQvd2VicA?x-oss-process=image/format,png)

WX20171213-184720@2x.png

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8yMDY0NDA0LWIwYmM5NGUzOTFkZGFlOGYucG5nP2ltYWdlTW9ncjIvYXV0by1vcmllbnQvc3RyaXB8aW1hZ2VWaWV3Mi8yL3cvMTIwMC9mb3JtYXQvd2VicA?x-oss-process=image/format,png)

WX20171213-184800@2x.png

----

**安装PHP**
由于Mac上默认安装过PHP，在10.12.6下默认为5.6
所以需要安装PHP7.0
brew update // 添加三方仓库 brew tap homebrew/dupes brew tap homebrew/versions brew tap homebrew/homebrew-php brew options php70 //查看安装选项 brew install php70 (--with-x 根据需要在安装选项中选择) php -v // 查看php版本，此时因为有默认PHP存在 所以还是显示先前的版本，需要下图的配置

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8yMDY0NDA0LWJmMDVhY2YzZWE4MGQyMWEucG5nP2ltYWdlTW9ncjIvYXV0by1vcmllbnQvc3RyaXB8aW1hZ2VWaWV3Mi8yL3cvMTIwMC9mb3JtYXQvd2VicA?x-oss-process=image/format,png)

WX20171213-190805@2x.png

在/usr/local/var/www中添加测试文件测试即可

----

**安装MySQL**
brew install mysql 输入以下命令进行安全安装 mysql_secure_installation Enter current password for root (enter for none): //默认没有密码，直接回车即可 > Change the root password? [Y/n] //是否更改root密码，选择是，然后输入并确认密码 > Remove anonymous users? [Y/n] //是否删除匿名用户，选择是 > Disallow root login remotely? [Y/n] //是否禁止远程登录，选择是 > Remove test database and access to it? [Y/n] //是否删除test数据库，选择是 > Reload privilege tables now? [Y/n] //是否重载表格数据，选择是

似乎这个安全安装只能进行一次。很难过。

此时基本环境搭配完成，等使用时在进行进一步的配置

----

1.25更新：
今天使用mysql的时候发现密码忘记了，试了很多种办法。
最终实现如下：
mysql.server stop //关闭mysql服务 //然后运行mysqld_safe ./mysqld_safe --skip-grant-tables & //若提示进程以及存在则使用命令终止进程 ps -A|grep mysql //查看有关进程 kill -9 xxx //xxx表示进程号

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8yMDY0NDA0LTNhNzkwMzZkZjY3ZGJjMWYucG5nP2ltYWdlTW9ncjIvYXV0by1vcmllbnQvc3RyaXB8aW1hZ2VWaWV3Mi8yL3cvMTIwMC9mb3JtYXQvd2VicA?x-oss-process=image/format,png)

WX20180125-201014@2x.png

 

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8yMDY0NDA0LTBkOWFlN2ZkMWJiZDllMWYucG5nP2ltYWdlTW9ncjIvYXV0by1vcmllbnQvc3RyaXB8aW1hZ2VWaWV3Mi8yL3cvMTIwMC9mb3JtYXQvd2VicA?x-oss-process=image/format,png)

image.png
此时重复./mysqld_safe --skip-grant-tables & 命令 回车后再输入mysql 进入数据库 直接输入 update mysql.user set authentication_string=password('123456') where user='root' and Host='localhost' 进行更改密码，提示成功后 退出数据库，并重启数据库即可

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8yMDY0NDA0LTgxMmJlYmUwYzgzZmNhNWYucG5nP2ltYWdlTW9ncjIvYXV0by1vcmllbnQvc3RyaXB8aW1hZ2VWaWV3Mi8yL3cvMTIwMC9mb3JtYXQvd2VicA?x-oss-process=image/format,png)

image.png

----

2.5更新
在nginx和php以及mysql安装成功后，忘记了php-fpm，所以无法访问php，此时在～目录下输入php70-fpm start
有些人会报出命令不存在。这是因为环境变量的问题。
网上很多都是用ln设置软连接的方式，而我选择了更直接了当的方式，去/etc的目录下更改paths文件，添加上你php70-fpm可执行文件的路径即可。
打开php-fpm服务后即可访问php文件。

如果出现File not Found，则将nginx.conf更改如下
再次之前此处的注释应该是全部取消掉的 将fastcgi_param SCRIPT_FILENAME /scripts$fastcgi_script_name; 替换成fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name; location ~ \.php$ { root html; //更改你的根目录即可 fastcgi_pass 127.0.0.1:9000; fastcgi_index index.php; fastcgi_param SCRIPT_FILENAME /$document_root$fastcgi_script_name; include fastcgi_params; }

 


