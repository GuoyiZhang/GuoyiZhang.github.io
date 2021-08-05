---
layout:     post
title:      【Vue】零基础学习Vue: 第17课 Vue的v-bind指令使用与设置及改变标签样式
subtitle:   【Vue】零基础学习Vue: 第17课 Vue的v-bind指令使用与设置及改变标签样式
date:       2019-06-29 16:58:45
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 前端
    - 零基础学习Vue
    - vue
---
>blog.getTitle() 

# 【Vue】零基础学习Vue: 第17课 Vue的v-bind指令使用与设置及改变标签样式


**v-bind指令 给标签绑定vue中定义的属性 ：
v-bind 缩写 ":"**

## []()[]()通过缩写 " : "给标签绑定class属性名称：

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>Document</title> <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script> <style> .bbb{ width:200px; height:200px; background-color:red; } .ccc{ width:200px; height:200px; border: 10px solid green; } </style> </head> <body> <div id="app"> <!-- class="arr"给添加class名 :class=bbbobj设置class名是否启用 不设置默认fasle不启用 --> <div class="arr" :class=bbbobj >啦啦啦</div> </div> <script> let vm = new Vue({ el:"/#app", data: { bbbobj:{ bbb:true, ccc:true }, //arr内的组员对应css样式中的class名 arr:["bbb","ccc"] } }) </script> </body> </html>

**运行结果如下：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416115632973.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)

## []()[]()还可以通过style属性直接设置属性：

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>Document</title> <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script> </head> <body> <div id="app"> <!-- 直接设置属性样式 --> <div :style="aaa" >啦啦啦</div> </div> <script> let vm = new Vue({ el:"/#app", data: { aaa:{ width:"200px", height:"200px", backgroundColor:"pink" } } }) </script> </body> </html>

**运行结果如下：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416115642380.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)

 

