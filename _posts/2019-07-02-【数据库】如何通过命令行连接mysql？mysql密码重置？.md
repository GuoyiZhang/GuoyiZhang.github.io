---
layout:     post
title:      【数据库】如何通过命令行连接mysql？mysql密码重置？
subtitle:   【数据库】如何通过命令行连接mysql？mysql密码重置？
date:       2019-07-02 10:44:51
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 数据库
    - mysql
---

>【数据库】如何通过命令行连接mysql？mysql密码重置？

# 【数据库】如何通过命令行连接mysql？mysql密码重置？


**目录**

[1. 如何通过命令行连接mysql数据库](#1.%20%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%E5%91%BD%E4%BB%A4%E8%A1%8C%E8%BF%9E%E6%8E%A5mysql%E6%95%B0%E6%8D%AE%E5%BA%93)

[2. mysql数据库基本命令](#2.%20mysql%E6%95%B0%E6%8D%AE%E5%BA%93%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4)

[3. mysql数据库修改密码](#3.%20mysql%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BF%AE%E6%94%B9%E5%AF%86%E7%A0%81)

----

### 1. 如何通过命令行连接mysql数据库

windows端：需要在命令行中进入mysql所在的目录下，进入bin目录下：

首先设置环境变量，（as: D:\WebServer\bin\mysql-5.7.19\bin）
mysql -hlocalhost -uroot -p

-u后面的为用户名名称 -p后面输入密码

![](https://img-blog.csdn.net/20180605142258488?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzNDE0NTA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

显示出这个时证明连接数据库成功

### 2. mysql数据库基本命令

show databases;    //显示存在的数据库

![](https://img-blog.csdn.net/20180605142758920?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzNDE0NTA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

use test;                //test为相对应的数据库名称，表示使用某个数据库

show tables;        //显示此数据库的所有表

![](https://img-blog.csdn.net/20180605142819384?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzNDE0NTA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
 
desc book;            //book为对应的表名称，此命令为展示表的字段详情

![](https://img-blog.csdn.net/20180605142831704?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzNDE0NTA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 

create database test;    //建数据库。名字为test
 
create table user(id int(4) not null primary key auto_increment,username char(20)not null,password char(20) not null);

//创建数据库，名为user
 
insert into user (1,'duoduo','921012');        //插入一条数据

select /* from user;        //查询所有的信息

### 3. mysql数据库修改密码

set password for root@localhost = password('/*/*/*/*/*/*/*/*');
