---
layout:     post
title:      【区块链 | 预言机】价格预言机的使用总结（一）：Chainlink篇
subtitle:   【区块链 | 预言机】价格预言机的使用总结（一）：Chainlink篇
date:       2022-08-19 13:38:56
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 价格预言机
    - 区块链零基础到实战教程
    - 区块链
---

>【区块链 | 预言机】价格预言机的使用总结（一）：Chainlink篇

# 【区块链 | 预言机】价格预言机的使用总结（一）：Chainlink篇


## 前言

价格预言机已经成为了 DeFi 中不可获取的基础设施，很多 DeFi 应用都需要从价格预言机来获取稳定可信的价格数据，包括借贷协议 **Compound、AAVE、Liquity**，也包括衍生品交易所 **dYdX、PERP** 等等。

目前最主流的价格预言机主要有 **Chainlink、UniswapV2、UniswapV3**，这几种价格预言机的接入方式和适用场景都不太一样，可以单独使用，也可以结合使用。鉴于不少同学还不知道这些预言机具体有哪些接入方式，也不了解背后的机制，更不清楚如何才能做到保证安全性的同时又能以最小的成本接入。下面，我将分享下我的经验总结，以供参考。

## Chainlink

先从 Chainlink 的价格预言机开始聊起，这应该是使用最广泛的价格预言机了。

其实，Chainlink 提供的产品不只是价格预言机，还有其他产品，包括 **Verifiable Random Numbers (VRF)、Call External APIs、Chainlink Keepers**。当然，使用最广泛的还是价格预言机，叫 **Data Feeds**。

**Chainlink Data Feeds** 目前已经支持了多条链，主要还是 **EVM** 链，包括 **Ethereum、BSC、Heco、Avalanche** 等，也包括 **Arbitrum、Optimism、Polygon** 等 L2 的链。另外，也支持了非 EVM 链，目前支持了 **Solana** 和 **Ter**

