---
layout:     post
title:      【区块链 | 默克尔树】如何利用Merkle实现空投，像Uniswap一样使用Merkle执行Airdrop
subtitle:   【区块链 | 默克尔树】如何利用Merkle实现空投，像Uniswap一样使用Merkle执行Airdrop
date:       2022-09-01 15:29:37
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 区块链零基础到实战教程
    - Uniswap技术架构指南
    - 区块链
    - 区块链
---

>【区块链 | 默克尔树】如何利用Merkle实现空投，像Uniswap一样使用Merkle执行Airdrop

# 【区块链 | 默克尔树】如何利用Merkle实现空投，像Uniswap一样使用Merkle执行Airdrop


如果你想直接跳过如何实现 Uniswap Airdrop，请继续阅读以下部分：**创建 Merkle Airdrop 的步骤**

![](https://img-blog.csdnimg.cn/img_convert/21194b615022267dd549c8542f29d15d.jpeg)

图片来自 https://ccoingossip.com/what-is-airdrop-in-crypto-world/

## Airdrop

Airdrop 是指项目决定向一组用户分发代币的事件。以下是实现 Airdrop 的一些潜在方法：

### 1. 管理员调用函数发送代币

在这种情况下，一个函数实现如下：
function airdrop(address address, uint256 amount) onlyOwner {   IERC20(token).transfer(account, amount); }

在这种场景下，所有者必须支付 gas 费才能调用该函数，如果地址列表很大，尤其是在 ETH 上，这将是不可持续的。

### 2. 在合约上存储白名单地址列表

您可能会实现一个映射，该映射 

mapping(address => some struct)
存储所有列入白名单的地址以及该地址是否已认领 Airdrop。同样，所有者也必须支付 gas 费用来存储合约的白名单地址列表。

## Merkle Airdrop

对于 Merkle Airdrop，实现了相同的目标并具有以下好处：

* 所有者只需支付 gas 费来创建合约并将 Merkle 根存储在合约上。
* 列入白

