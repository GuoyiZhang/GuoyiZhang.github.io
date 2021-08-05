# 【Vue】零基础学习Vue: 第15课 Watch 如何监听对象

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>Document</title> <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script> </head> <body> <div id="app"> <div>___{{obj}}__</div> </div> <script> let vm = new Vue({ el:"/#app", data: { obj:{ name:"小明", age: "18", sex: "男" } }, watch:{ obj(){ console.log("data 中数组arr被修改") } } }) </script> </body> </html>

**对象属性值改变时 运行结果如下：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416103602637.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)

**对象改变后 运行结果如下：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019041610363229.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)

 

### **那如何监听属性的变化呢！
答案如下：**

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>Document</title> <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script> </head> <body> <div id="app"> <div>___{{obj}}__</div> </div> <script> let vm = new Vue({ el:"/#app", data: { obj:{ name:"小明", age: "18", sex: "男" } }, watch:{ // obj(){ // console.log("data 中数组arr被修改") // }, obj:{ handler(){ //handler是特定的名称 console.log("data 中数组arr被修改") }, deep:true, //true表示深度监听 } } }) </script> </body> </html>

**/#/# 更改属性时 运行结果如下：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416103755807.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)


