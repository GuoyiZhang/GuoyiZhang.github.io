---
layout:     post
title:      【区块链 | Polygon】Polygon区块链PHP开发包-使用PHP语言开发Polygon
subtitle:   【区块链 | Polygon】Polygon区块链PHP开发包-使用PHP语言开发Polygon
date:       2022-05-09 10:42:03
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 区块链零基础到实战教程
    - 区块链
    - ETH
    - php
    - 区块链
    - polygon
---

>【区块链 | Polygon】Polygon区块链PHP开发包-使用PHP语言开发Polygon

# 【区块链 | Polygon】Polygon区块链PHP开发包-使用PHP语言开发Polygon

Polygon PHP开发包适用于为PHP应用快速增加对Polygon区块链数字资产的支持能力， 即支持使用自有Polygon区块链节点的应用场景，也支持基于Polygon区块链官方节点API服务的 轻量级部署场景。官方下载地址：[Polygon PHP开发包](http://sc.hubwiz.com/codebag/polygon-php/ "Polygon PHP开发包")。

[]()

## 1、开发包概述

Polygon PHP开发包主要包含以下特性：

* 支持Polygon区块链原生PHP转账交易及余额查询
* 支持Polygon链上智能合约的部署与交互，支持ERC20/ERC721/ERC1155转账交易及到账跟踪
* 支持Polygon链上交易的离线签名，避免泄露私钥
* 支持使用自有节点或第三方节点，例如Polygon官方提供的公共节点

Polygon PHP软件包运行在 **Php 7.1+** 环境下，当前版本1.0.0，主要类/接口及关系如下图所示：

![](https://img-blog.csdnimg.cn/img_convert/aaef9be6d89bef6e6c48f1410af28f07.png)

Polygon PHP开发包的主要代码文件清单如下：
代码文件 说明 polygon.php/src/Kit.php Polygon PHP开发包入口类 polygon.php/src/Erc2
