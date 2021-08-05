# 【Vue】零基础学习Vue: 第24课 Vue子组件触发父主件方法并传递参数：子组件$emit发射事件


### 实现代码如下：

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>$emit发射事件</title> <!-- 引入vue --> <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script> </head> <body> <div id="app"> <!-- 5.子组件$emit发射事件,传递参数触发父组件change方法 --> <son @xxx="change"></son> </div> <!-- 3.子组件son --> <template id="son"> <!-- 子主件son设置点击事件 执行子组件fn方法 fn方法发射事件给父主件并传递子组件参数--> <div @click="fn">我是子组件</div> </template> <script> //1.定义子组件 let son = { template:'/#son', //子组件的数据 data(){ return { msg:'我是子组件的数据' } }, //4.子组件定义方法函数 methods:{ fn(){ //把子组件的数据给父组件 //$emit 向父组件发射一个事件 发射事件之后 父组件可以立马监控到 //xxx表示事件名任意 this.$emit('xxx',this.msg) } } } let vm = new Vue({ el:'/#app', //2.注册子主件 components:{ son }, //6.设置方法 methods:{ change(value){ console.log("子组件传过来的数据msg:"+value) console.log('子组件发射的xxx事件 我检测到了') } } }) </script> </body> </html>

## 运行结果如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190421095528490.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)

