---
layout:     post
title:      【Java】Java中将xml文件转化为json的两种方式
subtitle:   【Java】Java中将xml文件转化为json的两种方式
date:       2019-12-16 15:01:21
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
---

>【Java】Java中将xml文件转化为json的两种方式

# 【Java】Java中将xml文件转化为json的两种方式


最近有个需求，要将xml转json之后存储在redis中，找来找去发现整体来说有两种方法，使用json-lib包中的net.sf.json或者使用org.json,这里将两种方式的实现代码写下来记录一下，以后方便拿来直接用了，省的来回找了。

第一种方式json-lib,这种方式需要的依赖包比较多，具体需要以下jar包这个从网上下载既可以了或者是利用Maven指定好依赖即可
![这里写图片描述](https://img-blog.csdn.net/20170729140630778?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYTUzMjY3MjcyOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

实现代码具体见下
public class Test { public static void ConvertXMLtoJSON() { InputStream is = Test.class.getResourceAsStream("student.xml"); String xml; try { xml = IOUtils.toString(is); System.out.println(xml); XMLSerializer xmlSerializer = new XMLSerializer(); JSON json = xmlSerializer.read(xml); System.out.println(json); System.out.println(json.toString(0)); } catch (IOException e) { e.printStackTrace(); } } public static void main(String[] args) { Test.ConvertXMLtoJSON(); } }

简单解释下该代码，

1. 这里通过Class的getResourceAsStream方法获得指定文件的输入流，这里指定参数没有带/,表示Test类与xml文件在同一级目录下，如果有/那么是从根目录进行获取的，
1. 之后利用IOUtils的toString方法将该输入流转化为xml格式的字符串输出，调用XMLSerializer的read方法接受xml格式的字符串，将其转化为JSON对象
1. 这里实际上输出json对象和调用json对象的toString方法输出的形式在控制台展示的是一样的

这里随便写了一个xml文件
<student name="zhangsan"> <sex>man</sex> <age>18</age> </student>

对应的输出的json

{"@name":"zhangsan","sex":"man","age":"18"}

这里只需要给出一个符合标准格式的xml文件即可，十分方便，如果是一个标签的属性那么会加上前缀@符号　　

另外一种方式是使用org.json来实现，这种方式更简单，只需要两个jar包即可，下载地址http://mvnrepository.com/artifact/org.json/json，随便下载一个使用比较多的jar包版本即可，具体实现代码见下
public class JsonUtils {     public static String xml2jsonString() throws JSONException, IOException {         InputStream in = JsonUtils.class.getResourceAsStream("student.xml");         String xml = IOUtils.toString(in);         JSONObject xmlJSONObj = XML.toJSONObject(xml);         return xmlJSONObj.toString();     }     public static void main(String[] args) throws JSONException, IOException {         String string = xml2jsonString();         System.out.println(string);     } }

简单对比一下使用json-lib的实现方式，前面的代码基本一致，区别是这里使用的是org.json.XML类，调用的是toJSONObject方法，接受的是一个xml格式的字符串，生成一个JSONObject对象，这里也是一样，调不调用jsonobject的toString方法输出效果都一样，xml文件内容一样，输出的格式见下

{"student":{"sex":"man","name":"zhangsan"}}

最后总结一下：

1. json-lib依赖的jar包很多，需要全部集齐，org.json仅仅需要两个jar包即可实现，一个org.json另一个是commons-io
1. 两者输出的xml格式不同，前者不带根标签需要手动添加，会区别标签的属性和子标签，后者带有根标签，标签的属性和子标签不会区分对待，因此根据自己的实际情况自行选择修改即可。

