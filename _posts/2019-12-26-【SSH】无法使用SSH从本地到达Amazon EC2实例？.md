---
layout:     post
title:      【SSH】无法使用SSH从本地到达Amazon EC2实例？
subtitle:   【SSH】无法使用SSH从本地到达Amazon EC2实例？
date:       2019-12-26 21:33:30
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 服务器
---

>【SSH】无法使用SSH从本地到达Amazon EC2实例？

# 【SSH】无法使用SSH从本地到达Amazon EC2实例？


无法SSH入Amazon EC2实例，这似乎是非常常见的问题，但我已尝试在所有可用文档中建议的所有内容，其他任何人都知道下面缺少的内容？

* 创建新的EC2实例并下载该

.pem
文件
* 在EC2实例中创建新的入站规则安全组允许我的本地IP
* 在EC2实例网络ACL中创建一个新的入站规则，以允许我的本地IP
* 在EC2实例网络ACL中创建新的出站规则以访问我的本地IP
* 确保VPC路由连接到互联网网关
* 确保EC2实例已连接到正确的安全组

毕竟，当我尝试从本地机器ssh我连接超时，还有什么我必须做我也已经禁用防火墙和测试只是incase

 ssh -vvv -i Fvt.pem root@3.112.226.217

