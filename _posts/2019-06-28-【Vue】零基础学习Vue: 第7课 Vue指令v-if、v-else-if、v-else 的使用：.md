# 【Vue】零基础学习Vue: 第7课 Vue指令v-if、v-else-if、v-else 的使用：

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>Document</title> <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script> </head> <body> <div id="app"> <!-- a=1所以前两个都是fasle 所以只显示最后一个div--> <div v-if="a>2">我是v-if</div> <div v-else-if="a>3">我是v-else-if</div> <div v-else>我是v-else</div> </div> <script> let vm = new Vue({ el:"/#app", data:{ a:1, } }) </script> </body> </html>

## 运行结果如下：

我是v-else
