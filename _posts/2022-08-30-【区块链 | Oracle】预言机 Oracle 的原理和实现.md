---
layout:     post
title:      【区块链 | Oracle】预言机 Oracle 的原理和实现
subtitle:   【区块链 | Oracle】预言机 Oracle 的原理和实现
date:       2022-08-30 18:47:43
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 区块链零基础到实战教程
    - 价格预言机
    - oracle
    - 区块链
---

>【区块链 | Oracle】预言机 Oracle 的原理和实现

# 【区块链 | Oracle】预言机 Oracle 的原理和实现


# Oracle

oracle 翻译是预言机，英文中的意思是预卜先知，知晓消息的意思。在区块链里用于合约获取链外的数据。例如你想把比特币转换成美元，如果在链上进行，那么就需要从链外获取比特币和美元的汇率，例如[price feed oracles](https://developer.makerdao.com/feeds/ "price feed oracles")**。但是以太坊是封闭的系统，直接与外界交互很容易破坏 EVM 安全性，因此才用了预言机作为中间层，沟通链上和链外。详细可见**[chainlink的文档](https://chain.link/education/blockchain-oracles "chainlink的文档")**和**[官方文档](https://ethereum.org/en/developers/docs/oracles/ "官方文档")。

在以太坊上，******oracle 是已经部署的智能合约和链外组件，它可以查询 API 提供的信息，然后给其他合约发消息，更新合约的数据******。但是只相信唯一的数据源也是很不可靠的方式，通常是多个数据源。我们可以自己创建，也可以直接使用服务商提供的服务。

**一般 oracle 机制如下：**

1. **到了需要链外数据的时候，合约触发事件。**
1. **链外的接口监听事件的日志。**
1. **链外接口处理事务，然后交易的方式返回数据给合约。**

