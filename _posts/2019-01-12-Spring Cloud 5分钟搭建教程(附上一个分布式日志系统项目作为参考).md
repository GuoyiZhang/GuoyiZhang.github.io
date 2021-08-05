---
layout:     post
title:      Spring Cloud 5分钟搭建教程(附上一个分布式日志系统项目作为参考)
subtitle:   Spring Cloud 5分钟搭建教程(附上一个分布式日志系统项目作为参考)
date:       2019-01-12 15:24:12
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 架构学习
    - 高并发系统设计
---
>blog.getTitle() 

# Spring Cloud 5分钟搭建教程(附上一个分布式日志系统项目作为参考)


https://github.com/leoChaoGlut/log-sys

上面是我基于Spring Cloud ,Spring Boot 和 Docker 搭建的一个分布式日志系统.

目前已在我司使用. 想要学习Spring Cloud, Spring Boot以及Spring 全家桶的童鞋,可以参考学习,如果觉得好,star 一下吧~

 

<<<< 20170602 <<<< 

新增Spring Cloud [ Bus, Sleuth, Config, Stream ]教程:

Github: https://github.com/leoChaoGlut/spring-cloud-tutorial

>>>> 20170602 >>>> 

 

<<<< 20170608 <<<< 

Ribbon源码解析及常见问题: http://blog.csdn.net/lc0817/article/details/72886721

>>>> 20170608 >>>> 

 

## 1.前言:

1.1.以下内容是我通过阅读官方文档,并成功实践后的经验总结,希望能帮助你更快地理解和使用Spring Cloud. 

1.2.默认读者已经熟练掌握Spring 全家桶,Spring Boot和注解开发.

1.3.陆续更新

 

## 2.开发环境: @Deprecated

2.1.开发工具:idea

2.2.开发环境:jdk1.7

2.3.Spring版本:

2.3.1.Spring Boot :1.4.0 release

2.3.2.Spring Cloud : Camden SR2

 

## 3.demo:(献给急于速成的各位大兄弟): demo地址: https://github.com/leoChaoGlut/spring-cloud-demo

### 3.1.服务注册demo:

3.1.1.创建工程模块,如图所示

3.1.2.将官方提供的maven依赖,加入pom.
<?xml version="1.0" encoding="UTF-8"?> <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"> <modelVersion>4.0.0</modelVersion> <groupId>demo</groupId> <artifactId>spring-cloud-demo</artifactId> <packaging>pom</packaging> <version>1.0-SNAPSHOT</version> <modules> <module>discovery</module> <module>service0</module> <module>service1</module> </modules> <!--以下dependency来自官方--> <parent> <groupId>org.springframework.boot</groupId> <artifactId>spring-boot-starter-parent</artifactId> <version>1.4.0.RELEASE</version> </parent> <dependencyManagement> <dependencies> <dependency> <groupId>org.springframework.cloud</groupId> <artifactId>spring-cloud-dependencies</artifactId> <version>Camden.SR2</version> <type>pom</type> <scope>import</scope> </dependency> </dependencies> </dependencyManagement> <dependencies> <dependency> <groupId>org.springframework.cloud</groupId> <artifactId>spring-cloud-starter-config</artifactId> </dependency> <dependency> <groupId>org.springframework.cloud</groupId> <artifactId>spring-cloud-starter-eureka</artifactId> </dependency> <dependency> <groupId>org.springframework.boot</groupId> <artifactId>spring-boot-devtools</artifactId> <optional>true</optional> </dependency> </dependencies> </project>

3.1.3.如图步骤,完成Discovery
3.1.4.如图步骤完成Service0,Service1类似

3.1.5.简单到爆炸有没有...........,接下来先启动Discovery,然后启动Service0和Service1

3.1.6.打开浏览器,访问 localhost:8080 ,8080是Discovery里配置的端口号.一切顺利的话,可以看到:

3.1.7.已经成功注册了service0,service1两个服务

### 3.2.网关demo: 光是注册了服务还不行,这里可以再配一个网关,让服务调用有统一的入口. 

3.2.1.通过上图配置后,首先启动Discovery,其次的服务和网关启动顺序随意.通过访问localhost:8083/service0/service0,即可看到,gateway帮我们转发了请求.

### 3.3.Feign:一个可以把远程服务提供方的 rest 接口变成本地方法调用的Spring Cloud组件

举个栗子:

现在有2个服务,service0, service1

service0提供了一个test接口,

那么这时候,如果service1需要的调用service0,除了通过网关(zuul)调用,还可以使用Feign,来把service0的远程接口,变为本地方法调用.如图:

## 4.feign + ribbon + hystrix

简介:

hystrix: 以切面为原理,可以在不入侵业务代码的情况下,给方法加上超时等指标,并且可以在超出设置的指标后,调用指定的fallback方法,进行失败回调处理.

ribbon: 客户端负载均衡, 我曾经也写了一个类似的东西(https://github.com/leoChaoGlut/ServiceDIscoveryAndRegistry/tree/master/doc),不过后来发现spring cloud已经有成熟的,现成的常用组件,所以就放弃了.哈哈.... 老式的,无注册中心的服务调用,是通过url来实现的,但是ribbon可以让我们只需要提供服务名,就可以调用到多实例的服务,并且在客户端做一个负载分发,减轻服务端负载的压力.

feign: 给你以Http的形式,带来RPC般的体验.

认真看图和代码,即可快速上手 feign + ribbon + hystrix 配置

细看spring cloud, feign,ribbon,hystrix的官方文档,加上源码的阅读,即可掌握如何使用spring cloud 配置 这三个组件.

## 5.分布式应用日志追踪: spring cloud sleuth:

http://blog.csdn.net/lc0817/article/details/72829935

## 6.分布式配置中心: Spring Cloud Config

http://blog.csdn.net/lc0817/article/details/72833007

## 7.消息总线: Spring Cloud Bus:

http://blog.csdn.net/lc0817/article/details/72836236

## 8.Netflix 基本组件:Feign Ribbon Hystrix 详细整合

http://blog.csdn.net/lc0817/article/details/72875195

### 9.流处理:Spring Cloud Stream

http://blog.csdn.net/lc0817/article/details/72956321
 

