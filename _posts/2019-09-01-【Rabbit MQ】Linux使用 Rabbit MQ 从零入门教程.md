# 【Rabbit MQ】Linux使用 Rabbit MQ 从零入门教程


### 1. 简介

**RabbitMQ **是部署最广泛的开源消息代理。

**AMQP** ，即Advanced Message Queuing Protocol，高级消息队列协议，是应用层协议的一个开放标准，为面向消息的中间件设计。消息中间件主要用于组件之间的解耦，消息的发送者无需知道消息使用者的存在，反之亦然。

AMQP的主要特征是面向消息、队列、路由（包括点对点和发布/订阅）、可靠性、安全。

**RabbitMQ** 是一个开源的AMQP实现，服务器端用Erlang语言编写，支持多种客户端，如：Python、Ruby、.NET、Java、JMS、C、PHP、ActionScript、XMPP、STOMP等，支持AJAX。用于在分布式系统中存储转发消息，在易用性、扩展性、高可用性等方面表现不俗。网站在： http://www.rabbitmq.com/ 上面有各种语言教程和实例代码

AMPQ协议为了能够满足各种消息队列需求，在概念上比较复杂，了解了这些概念，是使用好RabbitMQ的基础。

### 2. 六种工作模式

官网介绍：https://www.rabbitmq.com/getstarted.html

这里简单介绍下六种工作模式的主要特点：

简单模式：一个生产者，一个消费者

work模式：一个生产者，多个消费者，每个消费者获取到的消息唯一。

订阅模式：一个生产者发送的消息会被多个消费者获取。

路由模式：发送消息到交换机并且要指定路由key ，消费者将队列绑定到交换机时需要指定路由key

topic模式：将路由键和某模式进行匹配，此时队列需要绑定在一个模式上，“/#”匹配一个词或多个词，“/*”只匹配一个词。

### 3. 安装

3.1、安装Erlang

安装类库
yum -y install ncurses-devel yum -y install openssl-devel yum -y install unixODBC-devel yum -y install gcc-c++
 
yum install erlang

3.2 安装Rabbit MQ

yum install rabbitmq-server

### 4. 常用命令

/#1.查看 rabbit 状态 service rabbitmq-server status /#2.安装插件 /sbin/rabbitmq-plugins enable rabbitmq_management /#3.重启rabbitmq服务 service rabbitmq-server restart 到此,就可以通过http://ip:15672 使用guest,guest 进行登陆web页面了 /#4.停止开启服务： /#service rabbitmq-server stop /#service rabbitmq-server start 添加用户 rabbitmqctl add_user root 123456 设置用户角色 rabbitmqctl set_user_tags root administrator 查看用户 rabbitmqctl list_users 设置用户权限 rabbitmqctl set_permissions -p / root "./*" "./*" "./*" 查看RabbitMQ运行状态 rabbitmqctl status 关闭服务 rabbitmqctl stop 修改密码 rabbitmqctl change_password [Username] [Newpassword]

 


