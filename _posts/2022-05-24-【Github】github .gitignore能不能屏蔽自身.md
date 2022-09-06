---
layout:     post
title:      【Github】github .gitignore能不能屏蔽自身
subtitle:   【Github】github .gitignore能不能屏蔽自身
date:       2022-05-24 14:54:37
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - github
    - git
---

>【Github】github .gitignore能不能屏蔽自身

# 【Github】github .gitignore能不能屏蔽自身

修改了 .gitignore文件里面的内容 但是不想提交.gitnore文件，在.gitignore文件里面添加了.gitignore但是没有作用。请问有没有解决方案。

首先弄清楚你的需求，为什么要修改 

.gitignore
 文件，是不是有一些文件修改了，但不想提交到仓库中。
在 

git status
 时也不想看到它的存在。

如果是这样的话，不必修改 

.gitignore
 文件。
编辑当前项目下的 

.git/info/exclude
文件，然后将需要忽略提交的文件写入就行了，注意写入文件的路径是相对项目根目录而言的。

![](https://img-blog.csdnimg.cn/e1cefe81b5fd4f01bed86262e9b2683a.png)

 

希望能帮到你！

