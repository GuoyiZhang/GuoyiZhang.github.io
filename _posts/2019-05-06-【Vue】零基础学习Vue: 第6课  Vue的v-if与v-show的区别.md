---
layout:     post
title:      【Vue】零基础学习Vue: 第6课  Vue的v-if与v-show的区别
subtitle:   【Vue】零基础学习Vue: 第6课  Vue的v-if与v-show的区别
date:       2019-05-06 18:54:13
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 前端
    - 零基础学习Vue
    - vue
---

>【Vue】零基础学习Vue: 第6课  Vue的v-if与v-show的区别

# 【Vue】零基础学习Vue: 第6课  Vue的v-if与v-show的区别

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>Document</title> <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script> </head> <body> <div id="app"> <!-- 当a=true时 --> <div v-if="a">a我是v-if</div> <div v-show="a">a我是v-show</div> <!-- 当b=false时 --> <div v-if="b">b我是v-if</div> <div v-show="b">b我是v-show</div> </div> <script> let vm = new Vue({ el:"/#app", data:{ a:true, b:false, } }) </script> </body> </html>

## 运行结果如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190414143357382.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)

