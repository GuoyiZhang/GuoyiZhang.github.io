---
layout:     post
title:      【Linux】Linux常见错误 “cp: omitting directory”解决办法
subtitle:   【Linux】Linux常见错误 “cp: omitting directory”解决办法
date:       2019-12-20 18:03:24
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Linux
---

>【Linux】Linux常见错误 “cp: omitting directory”解决办法

# 【Linux】Linux常见错误 “cp: omitting directory”解决办法


### 问题描述

在Linux系统使用cp（复制命令）复制目录时，常出现错误“cp:omitting directory "dir" ”(dir是需要复制的目录名称)，是因为dir目录下存在其他目录或文件存在，不可只使用cp命令实现复制操作；

### 解决方法

使用cp命令时，加上 -r 选项，此选项指“递归持续复制，用於目录的复制行为”。 例如 cp -r dir ./usr

当执行删除操作时碰到类似的错误，也可使用递归式删除方式。

