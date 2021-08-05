---
layout:     post
title:      【Vue】零基础学习Vue: 第9课 Vue的 v-model指令,设置input表单输入的方法：
subtitle:   【Vue】零基础学习Vue: 第9课 Vue的 v-model指令,设置input表单输入的方法：
date:       2019-06-28 18:29:30
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 前端
    - 零基础学习Vue
    - vue
---
>blog.getTitle() 

# 【Vue】零基础学习Vue: 第9课 Vue的 v-model指令,设置input表单输入的方法：

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>Document</title> <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script> </head> <body> <div id="app"> <!-- v-model 输入框输入时时触发 将输入的值存放属性a中--> <input type="text" v-model="a">v-model输入时时触发<br> <!-- .lazy 输入框失去焦点时触发 --> <input type="text" v-model.lazy="a">lazy输入框失去焦点时触发<br> <!-- .number 输入框只能输入数字 --> <input type="text" v-model.number="a">number输入框只能输入数字<br> <!-- .trim 输入框只能输入数字 --> <input type="text" v-model.trim="a">trim输入框只能输入数字<br> <!-- 修饰符可同时使用 只能输入数字，前后无空格，失去焦点时触发--> <input type="text" v-model.number.trim.lazy="a">只能输入数字，前后无空格，失去焦点时触发 </div> <script> let vm = new Vue({ el:"/#app", data:{ a:1, } }) </script> </body> </html>

 


