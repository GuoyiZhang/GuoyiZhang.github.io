---
layout:     post
title:      【Vue】零基础学习Vue: 第20课 Vue定义子组件template的常见3种写法：
subtitle:   【Vue】零基础学习Vue: 第20课 Vue定义子组件template的常见3种写法：
date:       2019-06-29 17:36:27
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 前端
    - 零基础学习Vue
    - vue
---
>blog.getTitle() 

# 【Vue】零基础学习Vue: 第20课 Vue定义子组件template的常见3种写法：


### 第一种写法：

template:`<div><p>我是子组件3</p></div>`

### 第二种写法：

<template id="one1"> <div> <p>我是子组件1</p> </div> </template>

### 第三种写法：

<script type="text/x-template" id="two1"> <div> <p>我是子组件2</p> </div> </script>

那么子组件该怎么实现呢，我们定义3个不同的子组件来演示

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>vue组件</title> <!-- 引入vue --> <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script> </head> <body> <div id="app"> <!-- 4.引用子组件 --> <one></one> <two></two> <three></three> </div> <!--3.子组件写法1 子组件one --> <template id="one1"> <div> <p>我是子组件1</p> </div> </template> <!--3.子组件写法2 子组件two --> <script type="text/x-template" id="two1"> <div> <p>我是子组件2</p> </div> </script> <script> //1.定义子组件one let one = { template:'/#one1' } //1.定义子组件two let two = { template:'/#two1' } //1.定义子组件 three let three = { template:`<div><p>我是子组件3</p></div>`, //3.子组件写法3 子组件three } let vm = new Vue({ el:'/#app', //这是根组件 components:{ //2.注册组件 one, two, three } }) </script> </body> </html>

## 运行结果如下：

我是子组件1
我是子组件2
我是子组件3
