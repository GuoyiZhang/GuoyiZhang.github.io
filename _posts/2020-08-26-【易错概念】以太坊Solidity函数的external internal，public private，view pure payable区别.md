---
layout:     post
title:      【易错概念】以太坊Solidity函数的external/internal，public/private，view/pure/payable区别
subtitle:   【易错概念】以太坊Solidity函数的external/internal，public/private，view/pure/payable区别
date:       2020-08-26 15:41:54
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - ETH
    - 区块链
    - 区块链零基础到实战教程
    - 以太坊
    - 区块链
    - 数字货币
---

>【易错概念】以太坊Solidity函数的external/internal，public/private，view/pure/payable区别

# 【易错概念】以太坊Solidity函数的external/internal，public/private，view/pure/payable区别


![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMTkwNTc0LWIzN2JiM2NkNjYyMGJmOWEucG5nP2ltYWdlTW9ncjIvYXV0by1vcmllbnQvc3RyaXB8aW1hZ2VWaWV3Mi8yL3cvMTAwMC9mb3JtYXQvd2VicA?x-oss-process=image/format,png)

### 1. 函数类型：内部(internal)函数和外部(external)函数

函数类型是一种表示函数的类型。可以将一个函数赋值给另一个函数类型的变量，也可以将一个函数作为参数进行传递，还能在函数调用中返回函数类型变量。 函数类型有两类：- *内部（internal）*函数和 *外部（external）* 函数：

**内部函数只能在当前合约内被调用**（更具体来说，在当前代码块内，包括内部库函数和继承的函数中），因为它们不能在当前合约上下文的外部被执行。 调用一个内部函数是通过跳转到它的入口标签来实现的，就像在当前合约的内部调用一个函数。

外部函数由一个地址和一个函数签名组成，可以通过外部函数调用传递或者返回。

函数类型表示成如下的形式
function (<parameter types>) {internal|external} [pure|constant|view|payable] [returns (<return types>)]

与参数类型相反，返回类型不能为空 —— 如果函数类型不需要返回，则需要删除整个

returns (<re


