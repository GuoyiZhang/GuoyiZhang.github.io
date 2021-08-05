---
layout:     post
title:      如何使用redis保存PHP的session？【redis】
subtitle:   如何使用redis保存PHP的session？【redis】
date:       2019-03-09 10:34:05
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Redis
    - Linux
    - 架构学习
---
>blog.getTitle() 

# 如何使用redis保存PHP的session？【redis】


PHP 的会话默认是以文件的形式存在的，可以配置到 NoSQL 中，即提高了访问速度，又能很好地实现会话共享，，，爽歪歪！ 

 

配置方式如下：

### 方法一：修改 php.ini 的设置

1

2

session.save_handler = redis

session.save_path = 

"tcp://127.0.0.1:6379" 
session.hash_bits_per_character = 5 extension="redis.so" session.save_handler = redis session.save_path = "tcp://127.0.0.1:6379"

修改完之后，重启一下 php-fpm。

### 方式二：通过 ini_set() 函数设置

1

2

ini_set

(

"session.save_handler"

, 

"redis"

);

ini_set

(

"session.save_path"

, 

"tcp://127.0.0.1:6379"

);

如果配置文件 /etc/redis.conf 里设置了连接密码 requirepass，保存 session 的时候会报错，save_path 这样写 tcp://127.0.0.1:6379?auth=authpwd 即可。

### 测试代码：

1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

<?php

// 如果未修改php.ini下面两行注释去掉

// ini_set('session.save_handler', 'redis');

// ini_set('session.save_path', 'tcp://127.0.0.1:6379');

 

session_start();

$_SESSION

[

'sessionid'

] = 

'this is session content!'

;

echo
 

$_SESSION

[

'sessionid'

];

echo
 

'<br/>'

;

 

$redis
 

= 

new
 

redis();

$redis

->connect(

'127.0.0.1'

, 6379);

 

// redis 用 session_id 作为 key 并且是以 string 的形式存储

echo
 

$redis

->get(

'PHPREDIS_SESSION:'
 

. session_id());
