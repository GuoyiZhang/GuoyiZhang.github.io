# 【Vue】零基础学习Vue: 第14课 Watch 如何侦听数组

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>Document</title> <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script> </head> <body> <div id="app"> <div>___{{arr}}__</div> </div> <script> let vm = new Vue({ el:"/#app", data: { arr:[0,1,2,3,4] }, watch:{ arr(){ console.log("data 中数组arr被修改") } } }) </script> </body> </html>

**改变数组属性值时 运行结果如下：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416095807855.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)

**改变数组长度时 运行结果如下：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416095836107.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)

 

