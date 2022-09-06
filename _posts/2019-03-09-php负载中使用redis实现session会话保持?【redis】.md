---
layout:     post
title:      php负载中使用redis实现session会话保持?【redis】
subtitle:   php负载中使用redis实现session会话保持?【redis】
date:       2019-03-09 10:40:10
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Redis
    - 架构学习
---

>php负载中使用redis实现session会话保持?【redis】

# php负载中使用redis实现session会话保持?【redis】


首先要明确session和cookie的区别。浏览器端存的是cookie每次浏览器发请求到服务端是http 报文头是会自动加上你的cookie信息的。服务端拿着用户的cookie作为key去存储里找对应的value(session).
同一域名下的网站的cookie都是一样的。所以无论几台服务器,无论请求分配到哪一台服务器上同一用户的cookie是不变的。也就是说cookie对应的session也是唯一的。
所以，这里只要保证多台业务服务器访问同一个redis服务器(或集群)就行了。

修改php会话缓存机制改成Redis即可，这里有三种方式：

### 1，修改php的配置文件  修改php.ini文件

session.save_handler = redis session.save_path = "tcp://172.16.1.51:6379" //session.save_path = "tcp://172.16.1.51:6379?auth=123123"如果redis配置的密码需要写成这种方式，填写redis的密码 session.auto_start = 1

注释php-fpm.d/www.conf里面的两条内容

;php_value[session.save_handler] = files ;php_value[session.save_path] = /var/lib/php/session

更改完成之后一定要重启php-fpm服务

### 2，更改对应的代码文件

直接在对应的代码中加上下面两句话
ini_set("session.save_handler","redis"); ini_set("session.save_path","tcp://172.16.1.51:6379"); //如果redis设置了密码需要改下面一句为 //ini_set("session.save_path","tcp://172.16.1.51:6379?auth=123123");

测试

<?php //ini_set("session.save_handler", "redis"); //ini_set("session.save_path", "tcp://172.16.1.51:6379?auth=123123"); session_start(); //存入session $_SESSION['class'] = array('name' => 'toefl', 'num' => 8); //连接redis $redis = new redis(); $redis->connect('127.0.0.1', 6379); //检查session_id echo 'session_id:' . session_id() . '<br/>'; //redis存入的session（redis用session_id作为key,以string的形式存储） echo 'redis_session:' . $redis->get('PHPREDIS_SESSION:' . session_id()) . '<br/>'; //php获取session值 echo 'php_session:' . json_encode($_SESSION['class']);

### 3，自定义会话机制（目前不懂）

使用 session_set_save_handle 方法自定义会话机制，网上发现了一个封装非常好的类，我们可以直接使用这个类来实现我们的共享session操作。
<?php class redisSession{ //*/* /* 保存session的数据库表的信息 /*/ private $_options = array( 'handler' => null, //数据库连接句柄 'host' => null, 'port' => null, 'lifeTime' => null, 'prefix' => 'PHPREDIS_SESSION:' ); //*/* /* 构造函数 /* @param $options 设置信息数组 /*/ public function __construct($options=array()){ if(!class_exists("redis", false)){ die("必须安装redis扩展"); } if(!isset($options['lifeTime']) || $options['lifeTime'] <= 0){ $options['lifeTime'] = ini_get('session.gc_maxlifetime'); } $this->_options = array_merge($this->_options, $options); } //*/* /* 开始使用该驱动的session /*/ public function begin(){ if($this->_options['host'] === null || $this->_options['port'] === null || $this->_options['lifeTime'] === null ){ return false; } //设置session处理函数 session_set_save_handler( array($this, 'open'), array($this, 'close'), array($this, 'read'), array($this, 'write'), array($this, 'destory'), array($this, 'gc') ); } //*/* /* 自动开始回话或者session_start()开始回话后第一个调用的函数 /* 类似于构造函数的作用 /* @param $savePath 默认的保存路径 /* @param $sessionName 默认的参数名，PHPSESSID /*/ public function open($savePath, $sessionName){ if(is_resource($this->_options['handler'])) return true; //连接redis $redisHandle = new Redis(); $redisHandle->connect($this->_options['host'], $this->_options['port']); if(!$redisHandle){ return false; } $this->_options['handler'] = $redisHandle; // $this->gc(null); return true; } //*/* /* 类似于析构函数，在write之后调用或者session_write_close()函数之后调用 /*/ public function close(){ return $this->_options['handler']->close(); } //*/* /* 读取session信息 /* @param $sessionId 通过该Id唯一确定对应的session数据 /* @return session信息/空串 /*/ public function read($sessionId){ $sessionId = $this->_options['prefix'].$sessionId; return $this->_options['handler']->get($sessionId); } //*/* /* 写入或者修改session数据 /* @param $sessionId 要写入数据的session对应的id /* @param $sessionData 要写入的数据，已经序列化过了 /*/ public function write($sessionId, $sessionData){ $sessionId = $this->_options['prefix'].$sessionId; return $this->_options['handler']->setex($sessionId, $this->_options['lifeTime'], $sessionData); } //*/* /* 主动销毁session会话 /* @param $sessionId 要销毁的会话的唯一id /*/ public function destory($sessionId){ $sessionId = $this->_options['prefix'].$sessionId; // $array = $this->print_stack_trace(); // log::write($array); return $this->_options['handler']->delete($sessionId) >= 1 ? true : false; } //*/* /* 清理绘画中的过期数据 /* @param 有效期 /*/ public function gc($lifeTime){ //获取所有sessionid，让过期的释放掉 //$this->_options['handler']->keys("/*"); return true; } //打印堆栈信息 public function print_stack_trace() { $array = debug_backtrace (); //截取用户信息 $var = $this->read(session_id()); $s = strpos($var, "index_dk_user|"); $e = strpos($var, "}authId|"); $user = substr($var,$s+14,$e-13); $user = unserialize($user); //print_r($array);//信息很齐全 unset ( $array [0] ); if(!empty($user)){ $traceInfo = $user['id'].'|'.$user['user_name'].'|'.$user['user_phone'].'|'.$user['presona_name'].'++++++++++++++++\n'; }else{ $traceInfo = '++++++++++++++++\n'; } $time = date ( "y-m-d H:i:m" ); foreach ( $array as $t ) { $traceInfo .= '[' . $time . '] ' . $t ['file'] . ' (' . $t ['line'] . ') '; $traceInfo .= $t ['class'] . $t ['type'] . $t ['function'] . '('; $traceInfo .= implode ( ', ', $t ['args'] ); $traceInfo .= ")\n"; } $traceInfo .= '++++++++++++++++'; return $traceInfo; } } 在你的项目入口处调用上边的类：上边的方法等于是重写了session写入文件的方法，将数据写入到了Redis中。 初始化文件 init.php

<?php
require_once("redisSession.php");
$handler = new redisSession(array(
'host' => "172.16.1.51",
'port' => "6379"
));
$handler->begin();

// 这也是必须的，打开session，必须在session_set_save_handler后面执行
session_start();
测试 test.php ```php <?php // 引入初始化文件 include("init.php"); $_SESSION['isex'] = "Hello"; $_SESSION['sex'] = "Corwien"; // 打印文件 print_r($_SESSION); // ( [sex] => Corwien [isex] => Hello )

在Redis客户端使用命令查看我们的这条数据是否存在：

172.16.1.51:6379> keys /* 1) "first_key" 2) "mylist" 3) "language" 4) "mytest" 5) "pragmmer" 6) "good" 7) "PHPREDIS_SESSION:29a111bcs120sv48ibmmjqdag4" 8) "user:1" 9) "counter:__rand_int__" 10) "key:__rand_int__" 11) "tutorial-list" 12) "id:1" 13) "name" 127.0.0.1:6379> get PHPREDIS_SESSION:29a111bcs120sv48ibmmjqdag4 "sex|s:7:\"Corwien\";isex|s:5:\"Hello\";" 127.0.0.1:6379>

我们可以看到，我们的数据被保存在了Redis端了，键为：
PHPREDIS_SESSION:29a111bcs120sv48ibmmjqdag4.


