---
layout:     post
title:      【Vue】零基础学习Vue: 第1课 Windows下Node.js与npm的安装与配置
subtitle:   【Vue】零基础学习Vue: 第1课 Windows下Node.js与npm的安装与配置
date:       2019-05-06 10:55:25
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 前端
    - 零基础学习Vue
    - vue
    - npm
    - node
---

>【Vue】零基础学习Vue: 第1课 Windows下Node.js与npm的安装与配置

# 【Vue】零基础学习Vue: 第1课 Windows下Node.js与npm的安装与配置


1：先下载Node.js，网站[https://nodejs.org/en/](https://nodejs.org/en/)，左侧为稳定版，右侧为最新版，推荐稳定版

![](https://img-blog.csdn.net/20170116212752155?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVHJhZ3VlWnc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

2：Node.js安装，运行下载后的.msi文件，一路下一步就可以了，我选择的安装路径为E:\Program Files\nodejs,安装之后运行cmd,执行node -v 和 npm -v命令

![](https://img-blog.csdn.net/20170116213803113?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVHJhZ3VlWnc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如执行结果为上图所示出现版本号，说明安装成功，若出现不是内部命令的错误，则进入Node.js安装目录，执行此命令

2：配置npm的全局模块的存放路径以及cache的路径，我选择的路径使Node.js的安装路径，在此路径下建两个文件夹node_global 和 node_cache，现在的文件目录如下

![](https://img-blog.csdn.net/20170116215730047?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVHJhZ3VlWnc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

执行如下命令

![](https://img-blog.csdn.net/20170829204234837?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVHJhZ3VlWnc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

 

执行之后，使用命npm config get prefix,查看设置

 

4：尝试安装模块bower, 执行命令npm install bower-g, -g指安装到E:\Program Files\nodejs\node_global（global地址）

然后执行bower -v，发现如下错误

’bower’ 不是内部或外部命令，也不是可运行的程序或批处理文件。

5：配置环境变量

新建NODE_PATH: E:\Program Files\nodejs\node_global\node_modules（global地址）

![](https://img-blog.csdn.net/20170829204313272?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVHJhZ3VlWnc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

系统变量里的”PATH”关于Node.js的那一个修改为“E:\Program Files\nodejs\node_global\”（global）

 

然后重启cmd,网上大多数教程到此位置，在执行bower -v时就成功了，但我执行的结果却是

’node’ 不是内部或外部命令，也不是可运行的程序或批处理文件。

’npm’ 不是内部或外部命令，也不是可运行的程序或批处理文件。

’bower’ 不是内部或外部命令，也不是可运行的程序或批处理文件。

 

此时需将node.exe移到path设置的地址中，node_modules里的npm移到NODE_PATH设置的地址中，再执行bower -v，即可成功显示bower版本信息

![](https://img-blog.csdn.net/20170116222505088?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVHJhZ3VlWnc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

 

 

注： 主要有以下几点，以下几点正确，不用非得和上文设置一样

1：设置global路径，即全局安装时的路径

2：环境变量NODE_PATH值为 global路径下/node_modules

3：环境变量PATH 关于 node值为 global路径

4：node.exe要在PATH指定的路径下

5：npm要在NODE_PATH制定的路径下

