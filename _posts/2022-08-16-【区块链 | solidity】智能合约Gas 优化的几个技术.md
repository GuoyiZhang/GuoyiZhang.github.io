---
layout:     post
title:      【区块链 | solidity】智能合约Gas 优化的几个技术
subtitle:   【区块链 | solidity】智能合约Gas 优化的几个技术
date:       2022-08-16 17:23:19
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 区块链零基础到实战教程
---

>【区块链 | solidity】智能合约Gas 优化的几个技术

# 【区块链 | solidity】智能合约Gas 优化的几个技术

* 原文：[https://medium.com/coinmonks/smart-contracts-gas-optimization-techniques-2bd07add0e86](https://medium.com/coinmonks/smart-contracts-gas-optimization-techniques-2bd07add0e86 "https://medium.com/coinmonks/smart-contracts-gas-optimization-techniques-2bd07add0e86")
* 译文出自：[登链翻译计划](https://github.com/lbc-team/Pioneer "登链翻译计划")
* 译者：[翻译小组](https://learnblockchain.cn/people/412 "翻译小组")
* 校对：[Tiny 熊](https://learnblockchain.cn/people/15 "Tiny 熊")
* 本文永久链接：[learnblockchain.cn/article…](https://learnblockchain.cn/article/4515 "learnblockchain.cn/article…")

每次交易被发送到区块链上，必须支付Gas费用。消耗的Gas与交易所需的计算量有关，即：EVM执行交易所需的计算量（如果交易不涉及EVM，例如简单的以太币转账，Gas的数量是固定的）。

你可以设计和实现你的智能合约，使其具有**Gas效率**。在本博客中将讨论两种 "类型"的Gas ：

*

