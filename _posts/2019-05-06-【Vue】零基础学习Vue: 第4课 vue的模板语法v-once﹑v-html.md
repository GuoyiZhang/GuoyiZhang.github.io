# 【Vue】零基础学习Vue: 第4课 vue的模板语法v-once﹑v-html


## v-once 指令：

使用 v-once 指令，当数据改变时，插值处的内容不会更新。

**使用方式：**
<div v-once> </div>

**使用v-once与没有v-once的对比：**

<body> <!-- 获取的元素 --> <div id="app"> <!-- 没有使用v-once --> {{ xm }} <!-- 使用了v-once (加上背景颜色方便区分)--> <div style="background-color:pink" v-once> {{ xm }} </div> </div> <script> const app = new Vue({ el:"/#app", //获取元素 data:{ //data内存放vue的变量 xm:"小明", }, methods: { //methods存放vue内定义的方法 }, }) </script> </body>

**没有改变变量xm值时：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190327154934634.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)
**改变变量xm值时：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190327154955519.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)

## []()[]()v-html 指令：

双大括号会将数据解释为普通文本，而非 HTML 代码。使用 v-html 指令可以输出真正的 HTML 文档。

**使用方式：**
<div v-html="div"></div>

**使用v-html与没有v-html的对比：**

<body> <!-- 获取的元素 --> <div id="app"> <!-- 没有使用v-html --> {{ div }} <!-- 使用了v-html (加上背景颜色方便区分)--> <div style="background-color:pink" v-html="div"></div> </div> <script> const app = new Vue({ el:"/#app", //获取元素 data:{ //data内存放vue的变量 div:"<div>姓名：小明</div>", }, methods: { //methods存放vue内定义的方法 }, }) </script> </body>

运行结果如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190327164225320.png)


