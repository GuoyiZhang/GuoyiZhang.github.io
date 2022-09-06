---
layout:     post
title:      【区块链 | DEX】DEX交易聚合器怎么做？手动实现一个DEX交易聚合器
subtitle:   【区块链 | DEX】DEX交易聚合器怎么做？手动实现一个DEX交易聚合器
date:       2022-08-19 10:47:59
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Uniswap技术架构指南
    - 区块链零基础到实战教程
    - 区块链
---

>【区块链 | DEX】DEX交易聚合器怎么做？手动实现一个DEX交易聚合器

# 【区块链 | DEX】DEX交易聚合器怎么做？手动实现一个DEX交易聚合器


## 前言

目前，DeFi 赛道中，专门做 DEX 交易聚合的产品挺多的，以下是其中一些平台：

![](https://img-blog.csdnimg.cn/img_convert/99a5d337ab79c4609000db4586e69afe.png)

可以看到，这些平台都聚合了很多家 DEX，包括 **AMM**（自动做市商）模式的 DEX，也包括 **Orderbook** 模式的 DEX，主要功能都是为了将各个 DEX 的分散流动性整合到一起，提供最优的价格、最佳的深度和清晰简洁的界面。

这几天我也写了一个 DEX 交易聚合器，纯合约的。不过功能还比较简单，只聚合了 **UniswapV2** 和 **SushiSwap**，且只实现了从这两个平台中找出最优成交价来实现每笔交易。虽然只是个简单的交易聚合器，却也接连踩了好几个坑，这也暴露出了我的一些知识盲区。下面我就分享下在这过程中的一些经验和总结。

## 技术调研

既然接入的是 **UniswapV2** 和 **SushiSwap**，而且是从合约层面去接入的，所以第一步就是先要调研如何接入。

UniswapV2 的合约分为了两个项目：

* uniswap-v2-core
* uniswap-v2-periphery

**uniswap-v2-core** 的核心有三个合约：

* UniswapV2ERC20：UNI-V2 代币合

