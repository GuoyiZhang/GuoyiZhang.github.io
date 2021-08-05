# 【Vue】零基础学习Vue: 第8课 Vue的v-on点击事件阻止冒泡等：

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>Document</title> <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script> </head> <body> <div id="app"> <!-- 点击事件 点击触发fn函数 --> <div v-on:click="fn">点击</div> <!-- v-on: 缩写@ 也可点击触发fn事件 --> <div @click="fn">点击</div> <!-- 鼠标移动上去触发fn事件 --> <div @mouseenter="fn">触碰</div> <!-- 下面是click事件修饰符 --> <!-- .stop 阻止冒泡 --> <div @click.stop="fn">点击</div> <!-- .prevent 阻止默认点击事件 点击后不会跳转到百度页面--> <a href="www.baidu.com" @click.prevent>点击</a> <!-- .self 只当事件是从侦听器绑定的元素本身触发时才触发回调 --> <div @click.self="fn">点击</div> <!-- .once 此点击事件只触发一次 --> <div @click.once ="fn">点击</div> <!-- .left 只当点击鼠标左键时触发--> <div @click.left="fn">点击</div> <!-- .right 只当点击鼠标右键时触发 --> <div @click.right="fn">点击</div> <!-- .middle 只当点击鼠标中键时触发 --> <div @click.middle="fn">点击</div> </div> <script> let vm = new Vue({ el:"/#app", data:{ a:1, }, methods: { fn(){ console.log("111") } }, }) </script> </body> </html>

 


