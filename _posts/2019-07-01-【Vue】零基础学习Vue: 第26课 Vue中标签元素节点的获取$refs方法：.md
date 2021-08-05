---
layout:     post
title:      【Vue】零基础学习Vue: 第26课 Vue中标签元素节点的获取$refs方法：
subtitle:   【Vue】零基础学习Vue: 第26课 Vue中标签元素节点的获取$refs方法：
date:       2019-07-01 10:30:44
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 前端
    - 零基础学习Vue
    - vue
---

>【Vue】零基础学习Vue: 第26课 Vue中标签元素节点的获取$refs方法：

# 【Vue】零基础学习Vue: 第26课 Vue中标签元素节点的获取$refs方法：


 
ref=“定义标签获取名”
this.$refs.标签名 //获取对应的标签元素

this.$refs //获取所有定义好的标签

**首先我们使用代码来简单的获取一个标签：**

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>ref获取节点</title> <!-- 引入vue --> <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script> </head> <body> <div id="app"> <!-- ref="aaa":意思是将div这个标签设置为可以获取的标签且标签的获取名为aaa --> <div ref="aaa">我是一波段落</div> </div> <script> let vm = new Vue({ el:'/#app', mounted(){ //mounted生命周期函数(数据以挂在到页面上时触发) console.log(this.$refs.aaa) //这里我们打印获取结果 } }) </script> </body> </html>

### 运行结果如下：

### ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190423172510404.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)

[]()**使用this.$refs获取多个标签**

**接下来我们来一次性获取多个标签：**
<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>ref获取节点</title> <!-- 引入vue --> <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script> </head> <body> <div id="app"> <!-- 以下有两个标签获取名 重名 我们来看看运行结果吧 --> <!-- ref="aaa":意思是将div这个标签设置为可以获取的标签且标签名为aaa --> <div ref="aaa">我是div aaa段落</div> <p ref="aaa">我是p aaa段落</p> <span ref="xiaoming">我是span</span> <!-- 引入子组件 并且设置标签节点获取名isson --> <son ref="isson">我是组件</son> </div> <!-- 设置子组件son标签 --> <template id="son"> <div> <div>我是子组件div</div> <p>我是子组件p</p> </div> </template> <script> //定义子组件son let son = { template:'/#son' } let vm = new Vue({ el:'/#app', components:{ //在根组件内注册子组件son son }, mounted(){ //mounted生命周期函数(数据以挂在到页面上时触发) console.log(this.$refs) //获取所有定义可获取的标签名 } }) </script> </body> </html>

### 运行结果如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190423174324572.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)

### []()[]()接下来我们获取v-for遍历的标签：

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>ref获取节点</title> <!-- 引入vue --> <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script> </head> <body> <div id="app"> <!-- 通过给v-for遍历的标签能否获取到呢 往下看运行结果吧--> <ul> <li v-for="item in 5" ref="aaa">我是遍历出来的第{{item}}个li</li> </ul> </div> <script> let vm = new Vue({ el:'/#app', mounted(){ //mounted生命周期函数(数据以挂在到页面上时触发) console.log(this.$refs) //获取所有定义可获取的标签名 this.$refs.aaa[3].style.color = "red" //我们动态给第4个li设置以下字体颜色 } }) </script> </body> </html>

### 运行结果如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190423175739173.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)

## []()[]()本节课总结：

**this.$refs获取**
1.获取所有含有ref标记的单个节点 2.如果使用v-for获取相同的ref标记 则会获取到多个节点 3.如果ref标记到组件上 则获取的是整个组件

 

 

 

