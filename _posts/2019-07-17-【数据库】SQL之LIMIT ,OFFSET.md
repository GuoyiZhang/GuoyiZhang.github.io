---
layout:     post
title:      【数据库】SQL之LIMIT ,OFFSET
subtitle:   【数据库】SQL之LIMIT ,OFFSET
date:       2019-07-17 13:10:15
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 数据库
---

>【数据库】SQL之LIMIT ,OFFSET

# 【数据库】SQL之LIMIT ,OFFSET

SELECT prod_name FROM Products LIMIT 4 OFFSET 3;

LIMIT 4 OFFSET 3指示MySQL等DBMS返回从第3行（从0行计数）起的4行数据。**第一个数字是**检索的行数**，第二个数字是**指从哪儿开始**。**

MySQL和MariaDB支持简化版的LIMIT 4 OFFSET 3语句，即LIMIT 3,4。使用这个语法，   ,之前的值对应OFFSET， ,之后的值对应LIMIT **。**
** limit10000,20 的意思扫描满足条件的10020行，扔掉前面的10000行，返回最后的20行.**
 
[ 'conditions' => $conditions, 'bind' => $bind, 'order' => $order, 'offset' => intval($page - 1) /* $page_size, "limit" => $page_size, ]

 


