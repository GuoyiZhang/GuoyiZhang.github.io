# 【Vue】零基础学习Vue: 第12课 Vue计算属性computed与methods环境的区别


## computed环境内：

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>Document</title> <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script> </head> <body> <div id="app"> 姓<input type="text" v-model="a"> 名<input type="text" v-model="b"> <br> {{num}} {{c}} </div> <script> let vm = new Vue({ el:"/#app", data: { a:"", b:"", c:'' }, //computed 当视图重新渲染时 方法get不会执行 computed: { //存放的是时时计算属性 fn(){ console.log(1111) this.c = "啦啦啦" }, num(){ console.log(2222) return this.a+this.b } } }) </script> </body> </html>

## 运行结果如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190414203609149.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)

## []()[]()以下是在methods环境内：

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>Document</title> <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script> </head> <body> <div id="app"> 姓<input type="text" v-model="a"> 名<input type="text" v-model="b"> <br> {{num()}} {{c}} </div> <script> let vm = new Vue({ el:"/#app", data: { a:"", b:"", c:'' }, //methods 当视图重新渲染时 方法会再次 执行 methods: { fn(){ console.log(1111) this.c = "啦啦啦" }, num(){ console.log(2222) return this.a+this.b } } }) </script> </body> </html>

## 运行结果入下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190414204339907.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)

**小结：**
computed： 当某变量变化时视图重新渲染 调用其他变量的方法不会执行，只有自身内有变量变化的函数才会执行
methods： 当某变量变化时视图重新渲染 调用其他变量的方法会一起执行
