# 【Vue】零基础学习Vue: 第11课 Vue的各种keyup键盘点击事件：

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>Document</title> <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script> </head> <body> <div id="app"> <!-- 按enter键 抬起触发 --> <input type="text" @keyup.enter="fn">按键盘enter键触发<br> <input type="text" @keyup.13="fn">按键盘enter键触发<br> <!-- 按"删除"和"退格"键 抬起触发 --> <input type="text" @keyup.delete="fn">按键盘"删除"和"退格"键触发<br> <!-- 按tab键 抬起触发 --> <input type="text" @keyup.tab="fn">按键盘tab键触发<br> <!-- 按 esc 键 抬起触发 --> <input type="text" @keyup.esc="fn">按键盘 esc 键触发<br> <!-- 按 space 键 抬起触发 --> <input type="text" @keyup.space="fn">按键盘 space 键触发<br> <!-- 按 上 下 左 右 键 抬起触发 --> <input type="text" @keyup.up="fn">按键盘 up 键触发<br> <input type="text" @keyup.down="fn">按键盘 down 键触发<br> <input type="text" @keyup.left="fn">按键盘 left 键触发<br> <input type="text" @keyup.right="fn">按键盘 right 键触发<br> <!-- 只要 Ctrl 被按下并点击就触发 --> <button @click.ctrl="fn">按ctrl键并点击触发</button> <!--（exact精确按下某键） 有且只有 Ctrl 被按下并点击的时候才触发 --> <button @click.ctrl.exact="fn">按ctrl键并点击触发</button> </div> <script> let vm = new Vue({ el:"/#app", methods:{ fn(){ console.log(1111) } } }) </script> </body> </html>

 


