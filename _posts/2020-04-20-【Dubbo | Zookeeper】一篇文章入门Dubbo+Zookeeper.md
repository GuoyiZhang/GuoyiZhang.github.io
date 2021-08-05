---
layout:     post
title:      【Dubbo | Zookeeper】一篇文章入门Dubbo+Zookeeper
subtitle:   【Dubbo | Zookeeper】一篇文章入门Dubbo+Zookeeper
date:       2020-04-20 15:47:03
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Dubbo
    - 服务器
    - 架构学习
---

>【Dubbo | Zookeeper】一篇文章入门Dubbo+Zookeeper

# 【Dubbo | Zookeeper】一篇文章入门Dubbo+Zookeeper


**目录**

[1. Dubbo](#1.%20Dubbo)

[1.1 Dubbo简介](#1.1%C2%A0Dubbo%E7%AE%80%E4%BB%8B)

[1.2 Dubbo架构](#1.2%C2%A0Dubbo%E6%9E%B6%E6%9E%84)

[2. 服务注册中心Zookeeper](#2.%20%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%AD%E5%BF%83Zookeeper)

[2.1  Zookeeper简介](#2.1%C2%A0%20Zookeeper%E7%AE%80%E4%BB%8B)

[2.1.1 Zookeeper简介](#2.1.1%C2%A0Zookeeper%E7%AE%80%E4%BB%8B)

[2.1.2 Zookeeper安装与启动](#2.1.2%C2%A0Zookeeper%E5%AE%89%E8%A3%85%E4%B8%8E%E5%90%AF%E5%8A%A8)

[3. 简单案例](#3.%20%E7%AE%80%E5%8D%95%E6%A1%88%E4%BE%8B)

[3.1 服务提供者](#3.1%C2%A0%E6%9C%8D%E5%8A%A1%E6%8F%90%E4%BE%9B%E8%80%85)

[3.2 服务消费者](#3.2%20%E6%9C%8D%E5%8A%A1%E6%B6%88%E8%B4%B9%E8%80%85)

[3.3 Dubbo相关配置说明](#3.3%20Dubbo%E7%9B%B8%E5%85%B3%E9%85%8D%E7%BD%AE%E8%AF%B4%E6%98%8E)

[4. Dubbo管理控制台](#4.%20Dubbo%E7%AE%A1%E7%90%86%E6%8E%A7%E5%88%B6%E5%8F%B0)

[4.1 安装](#4.1%C2%A0%E5%AE%89%E8%A3%85)

[4.2 使用](#4.2%20%E4%BD%BF%E7%94%A8)

----

# 1. Dubbo

## 1.1 Dubbo简介

Apache Dubbo是一款高性能的Java RPC框架。其前身是阿里巴巴公司开源的一个高性能、轻量级的开源Java RPC框架，可以和Spring框架无缝集成。

**什么是****RPC****？**

RPC全称为remote procedure call，**即远程过程调用**。比如两台服务器A和B，A服务器上部署一个应用，B服务器上部署一个应用，A服务器上的应用想调用B服务器上的应用提供的方法，由于两个应用不在一个内存空间，不能直接调用，所以需要通过网络来表达调用的语义和传达调用的数据。

需要注意的是RPC并不是一个具体的技术，而是指整个网络远程调用过程。

RPC是一个泛化的概念，严格来说一切远程过程调用手段都属于RPC范畴。各种开发语言都有自己的RPC框架。Java中的RPC框架比较多，广泛使用的有RMI、Hessian、Dubbo等。

Dubbo官网地址：[http://dubbo.apache.org](http://dubbo.apache.org/)

Dubbo提供了三大核心能力：**面向接口的远程方法调用**，**智能容错和负载均衡**，以及**服务自动注册和发现**。

## 1.2 Dubbo架构

![dubbo-architucture](https://imgconvert.csdnimg.cn/aHR0cDovL2R1YmJvLmFwYWNoZS5vcmcvZG9jcy96aC1jbi91c2VyL3NvdXJjZXMvaW1hZ2VzL2R1YmJvLWFyY2hpdGVjdHVyZS5qcGc?x-oss-process=image/format,png)

节点角色说明
节点角色说明Provider
暴露服务的服务提供方Consumer
调用远程服务的服务消费方Registry
服务注册与发现的注册中心Monitor
统计服务的调用次数和调用时间的监控中心Container
服务运行容器

调用关系说明

1. 服务容器负责启动，加载，运行服务提供者。
1. 服务提供者在启动时，向注册中心注册自己提供的服务。
1. 服务消费者在启动时，向注册中心订阅自己所需的服务。
1. 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。
1. 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。
1. 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。

Dubbo 架构具有以下几个特点，分别是连通性、健壮性、伸缩性、以及向未来架构的升级性。

**1. 连通性**

* 注册中心负责服务地址的注册与查找，相当于目录服务，服务提供者和消费者只在启动时与注册中心交互，注册中心不转发请求，压力较小
* 监控中心负责统计各服务调用次数，调用时间等，统计先在内存汇总后每分钟一次发送到监控中心服务器，并以报表展示
* 服务提供者向注册中心注册其提供的服务，并汇报调用时间到监控中心，此时间不包含网络开销
* 服务消费者向注册中心获取服务提供者地址列表，并根据负载算法直接调用提供者，同时汇报调用时间到监控中心，此时间包含网络开销
* 注册中心，服务提供者，服务消费者三者之间均为长连接，监控中心除外
* 注册中心通过长连接感知服务提供者的存在，服务提供者宕机，注册中心将立即推送事件通知消费者
* 注册中心和监控中心全部宕机，不影响已运行的提供者和消费者，消费者在本地缓存了提供者列表
* 注册中心和监控中心都是可选的，服务消费者可以直连服务提供者

**2. 健壮性**

* 监控中心宕掉不影响使用，只是丢失部分采样数据
* 数据库宕掉后，注册中心仍能通过缓存提供服务列表查询，但不能注册新服务
* 注册中心对等集群，任意一台宕掉后，将自动切换到另一台
* 注册中心全部宕掉后，服务提供者和服务消费者仍能通过本地缓存通讯
* 服务提供者无状态，任意一台宕掉后，不影响使用
* 服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复

**3. 伸缩性**

* 注册中心为对等集群，可动态增加机器部署实例，所有客户端将自动发现新的注册中心
* 服务提供者无状态，可动态增加机器部署实例，注册中心将推送新的服务提供者信息给消费者

**4. 升级性**

当服务集群规模进一步扩大，带动IT治理结构进一步升级，需要实现动态部署，进行流动计算，现有分布式服务架构不会带来阻力。下图是未来可能的一种架构：

![dubbo-architucture-futures](https://imgconvert.csdnimg.cn/aHR0cDovL2R1YmJvLmFwYWNoZS5vcmcvZG9jcy96aC1jbi91c2VyL3NvdXJjZXMvaW1hZ2VzL2R1YmJvLWFyY2hpdGVjdHVyZS1mdXR1cmUuanBn?x-oss-process=image/format,png)

节点角色说明
节点角色说明Deployer
自动部署服务的本地代理Repository
仓库用于存储服务应用发布包Scheduler
调度中心基于访问压力自动增减服务提供者Admin
统一管理控制台Registry
服务注册与发现的注册中心Monitor
统计服务的调用次数和调用时间的监控中心

# 2. 服务注册中心Zookeeper

## 2.1  Zookeeper简介

### 2.1.1 Zookeeper简介

Zookeeper 是 Apache Hadoop 的子项目，是一个树型的目录服务，支持变更推送，适合作为 Dubbo 服务的注册中心，工业强度较高，可用于生产环境，并推荐使用 。

为了便于理解Zookeeper的树型目录服务，我们先来看一下我们电脑的文件系统(也是一个树型目录结构)：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvMTUwMDk1NC8yMDE5MDYvMTUwMDk1NC0yMDE5MDYyNTE5NDMxNDc2Mi01NTU1OTA0OTAucG5n?x-oss-process=image/format,png)

**Zookeeper树型目录服务：**

**![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvMTUwMDk1NC8yMDE5MDYvMTUwMDk1NC0yMDE5MDYyNTE5NDM0OTkyOC0xNTgzNjM1MjcwLnBuZw?x-oss-process=image/format,png)**

**流程说明：**

服务提供者(Provider)启动时: 向 

/dubbo/com.foo.BarService/providers
 目录下写入自己的 URL 地址

服务消费者(Consumer)启动时: 订阅 

/dubbo/com.foo.BarService/providers
 目录下的提供者 URL 地址。并向 

/dubbo/com.foo.BarService/consumers
 目录下写入自己的 URL 地址

监控中心(Monitor)启动时: 订阅 

/dubbo/com.foo.BarService
 目录下的所有提供者和消费者 URL 地址

### 2.1.2 Zookeeper安装与启动

使用资料中提供的windows版本zookeeper服务器进行安装即可

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvMTUwMDk1NC8yMDE5MDYvMTUwMDk1NC0yMDE5MDYyNTE5NDYxODE1My0yMDIzOTM1MjUyLnBuZw?x-oss-process=image/format,png)

进入安装路径的bin目录，双击zkServer.cmd即可启动zookeeper服务

# 3. 简单案例

Dubbo作为一个RPC框架，其最核心的功能就是要实现跨网络的远程调用。现在要创建两个应用，一个作为服务的提供者，一个作为服务的消费者。通过Dubbo来实现服务消费者远程调用服务提供者的方法。

项目目录结构如图

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvMTUwMDk1NC8yMDE5MDYvMTUwMDk1NC0yMDE5MDYyNjExMDIyNDkyOS0xNTQ2NjUyNzU3LnBuZw?x-oss-process=image/format,png)

## **3.1 服务提供者**

**1. 创建一个空工程，并创建maven工程（打包方式为war）dubbodemo_provider模块，在pom.xml文件中导入如下坐标**
<properties> <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding> <maven.compiler.source>1.8</maven.compiler.source> <maven.compiler.target>1.8</maven.compiler.target> <spring.version>5.0.2.RELEASE</spring.version> </properties> <dependencies> <dependency> <groupId>org.springframework</groupId> <artifactId>spring-context</artifactId> <version>${spring.version}</version> </dependency> <dependency> <groupId>org.springframework</groupId> <artifactId>spring-beans</artifactId> <version>${spring.version}</version> </dependency> <dependency> <groupId>org.springframework</groupId> <artifactId>spring-webmvc</artifactId> <version>${spring.version}</version> </dependency> <dependency> <groupId>org.springframework</groupId> <artifactId>spring-jdbc</artifactId> <version>${spring.version}</version> </dependency> <dependency> <groupId>org.springframework</groupId> <artifactId>spring-aspects</artifactId> <version>${spring.version}</version> </dependency> <dependency> <groupId>org.springframework</groupId> <artifactId>spring-jms</artifactId> <version>${spring.version}</version> </dependency> <dependency> <groupId>org.springframework</groupId> <artifactId>spring-context-support</artifactId> <version>${spring.version}</version> </dependency> <!-- dubbo相关 --> <dependency> <groupId>com.alibaba</groupId> <artifactId>dubbo</artifactId> <version>2.6.0</version> </dependency> <dependency> <groupId>org.apache.zookeeper</groupId> <artifactId>zookeeper</artifactId> <version>3.4.7</version> </dependency> <dependency> <groupId>com.github.sgroschupf</groupId> <artifactId>zkclient</artifactId> <version>0.1</version> </dependency> <dependency> <groupId>javassist</groupId> <artifactId>javassist</artifactId> <version>3.12.1.GA</version> </dependency> <dependency> <groupId>com.alibaba</groupId> <artifactId>fastjson</artifactId> <version>1.2.47</version> </dependency> </dependencies>

**2. 编写spring与dubbo整合的服务提供者spring配置文件applicationContext-dubboprovider.xml**

<?xml version="1.0" encoding="UTF-8"?> <beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo" xmlns:context="http://www.springframework.org/schema/context" xmlns:mvc="http://www.springframework.org/schema/mvc" xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"> <!--给当前服务提供者命名--> <dubbo:application name="dubbodemo_provider"/> <!--指定zookeeper注册中心的address和port，如果使用的是redis则address使用redis的address--> <dubbo:registry address="zookeeper://127.0.0.1:2181" ></dubbo:registry> <!--协议必须使用dubbo，端口号是提供一个可供消费者使用的端口--> <dubbo:protocol name="dubbo" port="20881"/> <!--开启注解扫描，使dubbo的注解生效--> <dubbo:annotation package="com.alibaba.provider"/> </beans>

**3. 在web.xml中配置项目加载监听器，并指定spring配置文件的路径**

<?xml version="1.0" encoding="UTF-8"?> <web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" version="2.5"> <!-- 监听器监听其他的spring配置文件 --> <context-param> <param-name>contextConfigLocation</param-name> <param-value>classpath/*:applicationContext-/*.xml</param-value> </context-param> <listener> <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class> </listener> </web-app>

**4. 编写service的接口和实现类**

Service接口代码
public interface HelloService { public String sayHello(String name); }

Service实现类代码

import com.alibaba.dubbo.config.annotation.Service; import com.alibaba.service.provider.HelloService; //此注解使用的阿里巴巴的dubbo注解 @Service public class HelloServiceImpl implements HelloService { @Override public String sayHello(String name) { return "hello@@"+name; } }

## 3.2 服务消费者

**1. 导入坐标**
<properties> <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding> <maven.compiler.source>1.8</maven.compiler.source> <maven.compiler.target>1.8</maven.compiler.target> <spring.version>5.0.2.RELEASE</spring.version> </properties> <dependencies> <dependency> <groupId>org.springframework</groupId> <artifactId>spring-context</artifactId> <version>${spring.version}</version> </dependency> <dependency> <groupId>org.springframework</groupId> <artifactId>spring-beans</artifactId> <version>${spring.version}</version> </dependency> <dependency> <groupId>org.springframework</groupId> <artifactId>spring-webmvc</artifactId> <version>${spring.version}</version> </dependency> <dependency> <groupId>org.springframework</groupId> <artifactId>spring-jdbc</artifactId> <version>${spring.version}</version> </dependency> <dependency> <groupId>org.springframework</groupId> <artifactId>spring-aspects</artifactId> <version>${spring.version}</version> </dependency> <dependency> <groupId>org.springframework</groupId> <artifactId>spring-jms</artifactId> <version>${spring.version}</version> </dependency> <dependency> <groupId>org.springframework</groupId> <artifactId>spring-context-support</artifactId> <version>${spring.version}</version> </dependency> <!-- dubbo相关 --> <dependency> <groupId>com.alibaba</groupId> <artifactId>dubbo</artifactId> <version>2.6.0</version> </dependency> <dependency> <groupId>org.apache.zookeeper</groupId> <artifactId>zookeeper</artifactId> <version>3.4.7</version> </dependency> <dependency> <groupId>com.github.sgroschupf</groupId> <artifactId>zkclient</artifactId> <version>0.1</version> </dependency> <dependency> <groupId>javassist</groupId> <artifactId>javassist</artifactId> <version>3.12.1.GA</version> </dependency> <dependency> <groupId>com.alibaba</groupId> <artifactId>fastjson</artifactId> <version>1.2.47</version> </dependency> </dependencies>

**2. 配置服务消费者的Spring配置文件applicationContext-consumer.xml**

<?xml version="1.0" encoding="UTF-8"?> <beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo" xmlns:context="http://www.springframework.org/schema/context" xmlns:mvc="http://www.springframework.org/schema/mvc" xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"> <!--开启包扫描，用来扫描所有的Spring注解--> <context:component-scan base-package="com.alibaba.web.controller.consumer"/> <!--开启MVC注解，使所有的MVC注解生效--> <mvc:annotation-driven/> <!--为服务消费者在向注册中心订阅服务时起个名字--> <dubbo:application name="dubbodemo_consumer"/> <!--连接到zookeeper注册中心--> <dubbo:registry address="zookeeper://127.0.0.1" port="2181"/> <!--开启包扫描，使dubbo的注解生效，让当前的被注解的类交给dubbo管理--> <dubbo:annotation package="com.alibaba.web.controller.consumer"/> </beans>

**3. 在web.xml配置前端控制器，加载spring的配置信息**

<servlet> <servlet-name>springmvc</servlet-name> <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class> <init-param> <param-name>contextConfigLocation</param-name> <param-value>classpath/*:applicationContext-/*.xml</param-value> </init-param> <load-on-startup>1</load-on-startup> </servlet> <servlet-mapping> <servlet-name>springmvc</servlet-name> <url-pattern>/*.do</url-pattern> </servlet-mapping>

**4. 编写控制器**

import com.alibaba.dubbo.config.annotation.Reference; import com.alibaba.provider.HelloService; import org.springframework.stereotype.Controller; import org.springframework.web.bind.annotation.RequestMapping; @Controller public class HelloController { //此处使用阿里巴巴的注解进行注入 @Reference HelloService helloService; @RequestMapping("/hello") public void hello(String name) { String result = helloService.sayHello(name); System.out.println(result); } }

**注意：服务提供者的接口必须存在于服务消费者的目录中，并且该接口的路径与服务提供者的路径必须保持一致，一模一样。**

## 3.3 Dubbo相关配置说明

**1. 包扫描：**

**服务提供者和服务消费者都需要配置，表示包扫描，作用是扫描指定包(****包括子包)****下的类**
<dubbo:annotation package="cn.itcast.service" />

**如果不使用包扫描，也可以通过如下配置的方式来发布服务**

<bean id="helloService" class="cn.itcast.service.impl.HelloServiceImpl" /> <dubbo:service interface="cn.itcast.HelloService" ref="helloService" />

**作为服务消费者，可以通过如下配置来引用服务**：

<!-- 生成远程服务代理，可以和本地bean一样使用helloService --> <dubbo:reference id="helloService" interface="cn.itcast.HelloService" />

上面这种方式发布和引用服务，一个配置项(dubbo:service、dubbo:reference)只能发布或者引用一个服务，如果有多个服务，这种方式就比较繁琐了。推荐使用包扫描方式。

**2. 协议：**
<dubbo:protocol name="dubbo" port="20880"/>

一般在服务提供者一方配置，可以指定使用的协议名称和端口号。

其中Dubbo支持的协议有：**dubbo、rmi、hessian、http、webservice、rest、redis**等。

**推荐使用的是dubbo协议**。

dubbo 协议采用单一长连接和 NIO 异步通讯，适合于小数据量大并发的服务调用，以及服务消费者机器数远大于服务提供者机器数的情况。不适合传送大数据量的服务，比如传文件，传视频等，除非请求量很低。

也可以在同一个工程中配置多个协议，不同服务可以使用不同的协议

**3. 启动时检查**
<dubbo:consumer check="false"/>

上面这个配置需要配置在服务消费者一方，如果不配置默认check值为true。**Dubbo 缺省会在启动时检查依赖的服务是否可用，不可用时会抛出异常，阻止 Spring 初始化完成，以便上线时，能及早发现问题**。可以通过将check值改为false来关闭检查。

**建议在开发阶段将check值设置为false**，在生产环境下改为true。

# 4. Dubbo管理控制台

我们在开发时，需要知道Zookeeper注册中心都注册了哪些服务，有哪些消费者来消费这些服务。我们可以通过部署一个管理中心来实现。其实管理中心就是一个web应用，部署到tomcat即可

## 4.1 安装

安装步骤：

（1）将资料中的dubbo-admin-2.6.0.war文件复制到tomcat的webapps目录下

（2）启动tomcat，此war文件会自动解压

（3）修改WEB-INF下的dubbo.properties文件，注意dubbo.registry.address对应的值需要对应当前使用的Zookeeper的ip地址和端口号
dubbo.registry.address=zookeeper://Ip:2181 ​ dubbo.admin.root.password=root ​ dubbo.admin.guest.password=guest

（4）重启tomcat

## 4.2 使用

操作步骤：

访问[http://localhost:8080/dubbo-admin-2.6.0/](http://localhost:8080/dubbo-admin-2.6.0/)，输入用户名(root)和密码(root)

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvMTUwMDk1NC8yMDE5MDYvMTUwMDk1NC0yMDE5MDYyNjExMjQ0NDMwMC0zNzY3NjM1MjUucG5n?x-oss-process=image/format,png)

 

