---
layout:     post
title:      【SpringBoot】SpringBoot + Spring data JPA使用方言（自定义函数、一些自带函数）
subtitle:   【SpringBoot】SpringBoot + Spring data JPA使用方言（自定义函数、一些自带函数）
date:       2019-12-24 17:08:36
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
---

>【SpringBoot】SpringBoot + Spring data JPA使用方言（自定义函数、一些自带函数）

# 【SpringBoot】SpringBoot + Spring data JPA使用方言（自定义函数、一些自带函数）


本人之前一直用mybitis，现在项目上使用jpa，给我带来了极大的方便，但也遇到一些问题。下列需求是这样的，我要根据id in 筛选出符合条件的数据，并将其中的某个字段，拼接起来返回。用原生sql比较简单，使用GROUP_CONCAT 函数即可。

SELECT GROUP_CONCAT(`name`) `names` FROM `face_machine` WHERE id in (1,2,3);
但使用jpa（非原生sql，因为很多地方都用到了，且使用原生sql，数据处理起来比较麻烦） 就会有问题了，我项目中jpa底层是使用的hibernate，所以要研究出如何使用hql语句来使用GROUP_CONCAT函数。

首先，dao层方法是这样的
    @Query(value = "select GROUP_CONCAT(fm.name) as names from FaceMachine fm where id in (?1)")
    String findNamesByIds(List<Integer> list);
这样直接使用是不行的，因为GROUP_CONCAT不是常用的函数。需要配置方言了。

要自定义一个MySQLDialect类

package com.ymkj.property.dialect;   import org.hibernate.dialect.MySQLDialect; import org.hibernate.dialect.function.StandardSQLFunction;   //*/*  /* @author zhengyue  /* @data 2019-08-06 11:16  /*/ public class MyMysqlDialect extends MySQLDialect{       public MyMysqlDialect() {         super();         registerFunction("GROUP_CONCAT", new StandardSQLFunction("GROUP_CONCAT"));       } }

然后需要在配置文件中添加配置，我的配置文件是xml

spring:     /# jap自动创建表及打印sql   jpa:     hibernate:       ddl-auto: update     show-sql: true     properties:       hibernate:         dialect: com.ymkj.property.dialect.MyMysqlDialect     database: mysql

主要是spring.properties.hibernate.dialect=com.ymkj.property.dialect.MyMysqlDialect （自定义的类）

//-------------------------------------------------------遇到以下问题------------------------------------------------------------------------------------//

刚开始按上述操作，启动并无问题，但若有需要新创建的表，就会出现问题，会报错，表也不会被创建出来。查了很多，都说是因为方言问题。确实是方言问题。下面附上解决方法：
package com.ymkj.property.dialect;   import org.hibernate.dialect.MySQLDialect; import org.hibernate.dialect.function.StandardSQLFunction;   //*/*  /* @author zhengyue  /* @data 2019-08-06 11:16  /*/ public class MyMysqlDialect extends MySQLDialect {       public MyMysqlDialect() {         super();         registerFunction("GROUP_CONCAT", new StandardSQLFunction("GROUP_CONCAT"));     } //此处是解决创建表报错问题     @Override     public String getTableTypeString() {         return " ENGINE=InnoDB DEFAULT CHARSET=utf8";     } }

 


