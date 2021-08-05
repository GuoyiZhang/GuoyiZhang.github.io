---
layout:     post
title:      【前端】javascript 去掉小数末尾多余的零
subtitle:   【前端】javascript 去掉小数末尾多余的零
date:       2019-07-18 09:09:52
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 前端
---

>【前端】javascript 去掉小数末尾多余的零

# 【前端】javascript 去掉小数末尾多余的零


 做的项目里需要去掉 小数末尾的零 就自己随手写了一个
var t = "10203000"; alert(cutZero(t)); //* 去掉double类型小数点后面多余的0 参数：old 要处理的字符串或double 返回值：newStr 没有多余零的小数或字符串 例： cutZero(123.000) -> 123 cutZero(123.0001) -> 123.0001 cutZero(10203000.0101000) -> 10203000.0101 cutZero(10203000) -> 10203000 /*/ function cutZero(old){ //拷贝一份 返回去掉零的新串 newstr=old; //循环变量 小数部分长度 var leng = old.length-old.indexOf(".")-1 //判断是否有效数 if(old.indexOf(".")>-1){ //循环小数部分 for(i=leng;i>0;i--){ //如果newstr末尾有0 if(newstr.lastIndexOf("0")>-1 && newstr.substr(newstr.length-1,1)==0){ var k = newstr.lastIndexOf("0"); //如果小数点后只有一个0 去掉小数点 if(newstr.charAt(k-1)=="."){ return newstr.substring(0,k-1); }else{ //否则 去掉一个0 newstr=newstr.substring(0,k); } }else{ //如果末尾没有0 return newstr; } } } return old; }

 


