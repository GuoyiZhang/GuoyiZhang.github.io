---
layout:     post
title:      【Vue】零基础学习Vue: 第16课 Vue的v-for遍历生成标签指令：
subtitle:   【Vue】零基础学习Vue: 第16课 Vue的v-for遍历生成标签指令：
date:       2019-06-29 16:55:17
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 前端
    - 零基础学习Vue
    - vue
---

>【Vue】零基础学习Vue: 第16课 Vue的v-for遍历生成标签指令：

# 【Vue】零基础学习Vue: 第16课 Vue的v-for遍历生成标签指令：


## 遍历数组：

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>Document</title> <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script> </head> <body> <div id="app"> <ul> <!-- ()内第一个参数item是组员值，index是组员下标 --> <li v-for="(item, index) in arr">{{index}}--{{item}}</li> </ul> </div> <script> let vm = new Vue({ el:"/#app", data: { arr:[1,2,3,4,5,6] }, }) </script> </body> </html>

**运行结果如下：**

● 0–1
● 1–2
● 2–3
● 3–4
● 4–5
● 5–6

## []()[]()v-for还可以遍历对象 如下：

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>Document</title> <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script> </head> <body> <div id="app"> <ul> <!-- ()内第一个参数item是属性值，index是属性名 --> <li v-for="(item, key) in obj">{{key}}--{{item}}</li> </ul> </div> <script> let vm = new Vue({ el:"/#app", data: { obj:{ name:"小明", age:18, sex:"女" } }, }) </script> </body> </html>

**运行结果如下：**

● name–小明
● age–18
● sex–女

**v-for遍历对象括号内还有第3个属性：**

**v-for(item,key,index) in obj →第3个属性是下标数**

## []()[]()v-for还可以遍历数字和字符串 如下：

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>Document</title> <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script> </head> <body> <div id="app"> <ul> <!-- ()内第一个参数item是组员值，index是组员下标 --> <li v-for="(item, index) in 5">{{index}}--{{item}}</li> <hr> <li v-for="(item, index) in 'abcdefg'">{{index}}--{{item}}</li> </ul> </div> <script> let vm = new Vue({ el:"/#app", data: { } }) </script> </body> </html>

**运行结果如下：**

0–1
1–2
2–3
3–4
4–5
——————————————————————————————
0–a
1–b
2–c
3–d
4–e
5–f
6–g

 

 

 

