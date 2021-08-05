---
layout:     post
title:      【Vue】零基础学习Vue: 第13课 Watch侦听属性、计算属性缓存computed、方法methods的区别
subtitle:   【Vue】零基础学习Vue: 第13课 Watch侦听属性、计算属性缓存computed、方法methods的区别
date:       2019-06-29 16:34:42
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 前端
    - 零基础学习Vue
    - vue
---

>【Vue】零基础学习Vue: 第13课 Watch侦听属性、计算属性缓存computed、方法methods的区别

# 【Vue】零基础学习Vue: 第13课 Watch侦听属性、计算属性缓存computed、方法methods的区别


**计算属性缓存computed：**
当页面内需要显示computed内的属性时，则其中某一计算属性的值变化，其他属性不会重新执行计算，只执行改变属性的方法，因为计算属性存在缓存。

**方法methods：**

当页面内需要使用methods内的方法时，则其中某一个方法内的属性变化，则其他方法都需要执行，计算出新的结果，因为方法内不存在缓存。

 **侦听属性watch:**

监控data中属性的变化，当需监控的属性变化时才会触发该监控函数

## 下面来看看侦听属性watch：

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>Document</title> <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script> </head> <body> <div id="app"> <div>___{{bbb}}___{{aaa}}__</div> <div>{{fullName}}___{{ccc}}</div> </div> <script> let vm = new Vue({ el:"/#app", data: { firstName:"小", lastName:"明", fullName:"", bbb:"1", aaa:"2", ccc:"" }, watch:{ bbb(){ console.log(11111) this.fullName = this.firstName + this.lastName; }, aaa(){ console.log(22222) this.ccc = this.bbb + this.aaa; } } }) </script> </body> </html>

### **运行结果如下：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416094044514.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)
 

### **当改变bbb值时运行结果如下：**

### ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416094115449.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)

 
 

 

 

 

