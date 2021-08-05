---
layout:     post
title:      【Dubbo】Spring 中 Dubbo配置实现负载均衡、集群环境详细资料
subtitle:   【Dubbo】Spring 中 Dubbo配置实现负载均衡、集群环境详细资料
date:       2020-04-20 14:49:19
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Dubbo
    - 架构学习
    - 高并发系统设计
    - java
    - 负载均衡
    - 分布式
---

>【Dubbo】Spring 中 Dubbo配置实现负载均衡、集群环境详细资料

# 【Dubbo】Spring 中 Dubbo配置实现负载均衡、集群环境详细资料


 

**目录**

[1. Dubbo实现负载均衡](#1.%20Dubbo%E5%AE%9E%E7%8E%B0%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1)

[四种负载均衡策略](#%E5%9B%9B%E7%A7%8D%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1%E7%AD%96%E7%95%A5)

[配置负载均衡级别的方法：](#%E9%85%8D%E7%BD%AE%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1%E7%BA%A7%E5%88%AB%E7%9A%84%E6%96%B9%E6%B3%95%EF%BC%9A)

[2. Dubbo 集群部署](#2.%20Dubbo%20%E9%9B%86%E7%BE%A4%E9%83%A8%E7%BD%B2)

----

## **1. Dubbo实现负载均衡**

**在用dubbo作为项目架构的时候，给consumer消费者用nginx提供了负载均衡策略和集群的实现，但是想了下，consumer再多，但是提供者还是一个，最后还不都是落到了这一个provider上面？**
**举个列子：**

一个饭店有1个后厨在做饭，

前台有100个点菜的服务员，

100个顾客来点餐，每个服务员都来告诉后厨做饭的，那么后厨...

**Dubbo实现负载均衡，一般是对服务的提供者来实现。我们的集群管理，也就是负载均衡，然后服务的消费者在请求消费的时候，通过一定的算法进行寻址（权重），可以参考下Nginx！**

### **四种负载均衡策略**

* ① 随机均衡算法 Random LoadBalance  【按权重设置随机概率。在一个截面上碰撞的概率较高，但调用越大分布越均匀】
* ② 权重轮循均衡算法 RoundRobin LoadBalance  【按公约后的权重设置轮询比例。但存在响应慢的服务提供者会累积请求，比如：第二台机器很慢，但没挂，当请求调到第二台时就卡在那，久而久之，所有请求都卡在调到第二台上。(针对此种情况，需要降低该服务的权值，以减少对其调用）】
* ③ 最少活跃调用数（权重）均衡算法 LeastActive LoadBalance  【活跃数指调用前后计数差，优先调用高的，相同活跃数的随机。使慢的提供者收到更少请求，因为越慢的提供者的调用前后计数差会越大】
* ④ 一致性Hash均衡算法 Hash ConsistentHash LoadBalance   【相同参数总是发送到同一个提供者，如果这个提供者挂掉了，它会根据它的虚拟节点，平摊到其它服务者，不会引起巨大的变动】

**注意/*：缺省为random 随机 （也就是说，不配置负载均衡策略的时候，默认为random）**

### **配置负载均衡级别的方法：**

可以给服务配置级别也可精确到每个方法的级别
**服务端服务级别 **配置：
<dubbo:service interface="接口名" loadbalance="roundrobin"/>

**服务端方法级别**配置：

<dubbo:service interface="接口名"> 　　<dubbo:method name="方法名" loadbalance="均衡策略名"/> </dubbo:service>

----

**客户端服务级别 **配置：
<dubbo:reference interface="" loadbalance="roundrobin" />

**客户端方法级别**配置：

<dubbo:reference interface="" loadbalance="roundrobin"> <dubbo:method name="方法名" loadbalance="均衡策略明"/> </dubbo:reference>

**我们也可以通过可视化界面管理平台来操作**

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvMTc2MDU3My8yMDE5MTAvMTc2MDU3My0yMDE5MTAxNDExMDg0NTg3MS0xNDg5NTc2NDQzLnBuZw?x-oss-process=image/format,png)

配置完负载均衡下面，就要来配置我们的dubbo集群了

## **2. Dubbo 集群部署**

具体的做法是对服务提供者的配置文件进行修改 ，配置文件里面的application name相同，dubbo则会认为是同一集群。

**部署多个集群只需要步骤：**

application name相同
<dubbo:application name="服务名"/>

协议端口需要修改不同

<dubbo:protocol name="dubbo" port="端口需要修改，不能重复"/>

下面是我配置服务的案例：

需求：配置一个集群环境，两个服务提供者

**provider01**
<?xml version="1.0" encoding="UTF-8"?> <beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo" xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd"> <!-- 配置应用名 注意：配置集群的情况下需要同一集群的name值相同 --> <dubbo:application name="dubbo-provider"/> <!-- 配置注册中心： address：注册中心的ip:port 注意：如果注册中心为ZooKeeper且为集群，那就要讲集群中所有的IP:PORT添加到该属性当中- protocol:注册中心类型 --> <dubbo:registry address="169.254.18.25:2181,169.254.18.25:2182,169.254.18.25:2183" protocol="zookeeper"/> <!-- 配置协议与端口 --> <dubbo:protocol name="dubbo" port="20880"/> <dubbo:protocol name="hessian" port="20881"/> <!-- 配置注册接口 ref:引用实现类，因为我们在实现类里面加了@Service扫描注解 --> <dubbo:service protocol="dubbo" interface="cn.arebirth.dubbo.service.UserDubboService" ref="userDubboServiceImpl" loadbalance="roundrobin"/> <dubbo:service protocol="hessian" interface="cn.arebirth.dubbo.service.CarDubboService" ref="carDubboServiceImpl" loadbalance="roundrobin"/> </beans>

**这个服务配置完后，打包发布到服务器运行，我们会在dubbo-admin的控制台上发现这样的内容**

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvMTc2MDU3My8yMDE5MTAvMTc2MDU3My0yMDE5MTAxNDExMjA0ODc2NS05OTMxMzQwMjgucG5n?x-oss-process=image/format,png)

**provider02**
<?xml version="1.0" encoding="UTF-8"?> <beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo" xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd"> <!-- 配置应用名 注意：配置集群的情况下需要同一集群的name值相同 --> <dubbo:application name="dubbo-provider"/> <!-- 配置注册中心： address：注册中心的ip:port 注意：如果注册中心为ZooKeeper且为集群，那就要讲集群中所有的IP:PORT添加到该属性当中- protocol:注册中心类型 --> <dubbo:registry address="169.254.18.25:2181,169.254.18.25:2182,169.254.18.25:2183" protocol="zookeeper"/> <!-- 配置协议与端口 --> <dubbo:protocol name="dubbo" port="20881"/> <dubbo:protocol name="hessian" port="20991"/> <!-- 配置注册接口 ref:引用实现类，因为我们在实现类里面加了@Service扫描注解 --> <dubbo:service protocol="dubbo" interface="cn.arebirth.dubbo.service.UserDubboService" ref="userDubboServiceImpl" loadbalance="roundrobin"/> <dubbo:service protocol="hessian" interface="cn.arebirth.dubbo.service.CarDubboService" ref="carDubboServiceImpl" loadbalance="roundrobin"/> </beans>

**我们在把provider02打包发布到服务器上运行，我们会看到我们的服务均已注册成功完毕，此时也意味着集群搭建完毕了**

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvMTc2MDU3My8yMDE5MTAvMTc2MDU3My0yMDE5MTAxNDExMjI1MDY3Ny0yMjI2ODEwOTAucG5n?x-oss-process=image/format,png)

**仔细观察我们上边的配置文件不难发现，其实就是一个端口不同，其余都是相同的，**

**ps：**

搭建集群环境注意：

同一集群环境中应用名必须一致

端口必须不同

