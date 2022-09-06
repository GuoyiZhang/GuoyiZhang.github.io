---
layout:     post
title:      【区块链 | ENS】以太坊（Ethereum）中的ENS架构中的反向注册器（反向解析）是什么？
subtitle:   【区块链 | ENS】以太坊（Ethereum）中的ENS架构中的反向注册器（反向解析）是什么？
date:       2022-06-15 14:33:21
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - ENS技术架构指南【系统分析】
    - 区块链
    - 以太坊
    - p2p
---

>【区块链 | ENS】以太坊（Ethereum）中的ENS架构中的反向注册器（反向解析）是什么？

# 【区块链 | ENS】以太坊（Ethereum）中的ENS架构中的反向注册器（反向解析）是什么？


# ENS 反向注册器

[源代码](https://github.com/ensdomains/ens/blob/master/contracts/ReverseRegistrar.sol "源代码")

ENS 中的反向解析是指从 **以太坊地址（比如 0x1234…）到 ENS 名称的映射 **，它通过一个特定的名称空间（_.addr.reverse_）来实现。这个名称空间由一个专用注册器拥有和控制，该注册器可以接受任何人的调用，并根据调用者的地址为其分配子名称。

例如，账户 *0x314159265dd8dbb310642f98f50c066173c1259b* 可以通过调用声明 *314159265dd8dbb310642f98f50c066173c1259b.addr.reverse.* ，然后为其配置一个解析器并指定元数据（比如此地址的规范 ENS 名称）。

反向注册器提供了声明反向记录的函数，同时为了提供一种给地址指定规范名称的方式，反向注册器还内置了一个便于配置最常用记录的函数。

反向注册器的详细信息请参阅 [EIP181](https://eips.ethereum.org/EIPS/eip-181 "EIP181")。

## 声明地址

function claim(address owner) public returns (bytes32);

通过在反向注册器中声明调用者的地址，将反向记录的所有权分配给 

owner
 ，相当于调用 

claimWithResolver(owner, 0)
 。

<
