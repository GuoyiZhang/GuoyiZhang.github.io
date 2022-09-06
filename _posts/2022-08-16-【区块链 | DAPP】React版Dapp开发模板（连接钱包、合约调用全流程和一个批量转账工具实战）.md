---
layout:     post
title:      【区块链 | DAPP】React版Dapp开发模板（连接钱包、合约调用全流程和一个批量转账工具实战）
subtitle:   【区块链 | DAPP】React版Dapp开发模板（连接钱包、合约调用全流程和一个批量转账工具实战）
date:       2022-08-16 17:19:14
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 区块链零基础到实战教程
    - 区块链
    - react.js
    - 前端
---

>【区块链 | DAPP】React版Dapp开发模板（连接钱包、合约调用全流程和一个批量转账工具实战）

# 【区块链 | DAPP】React版Dapp开发模板（连接钱包、合约调用全流程和一个批量转账工具实战）


# 框架版本

框架分为两个版本:
1.[Nextjs版](https://github.com/Verin1005/NextJs-Dapp-Template "Nextjs版")。几个大的Dapp(pancakeswap ,sushiswap,uniswap等等)用的都是这个技术栈，考虑到Dapp很多核心计算逻辑以及处理数据逻辑都在前端，建议使用这个Nextjs版本，在写业务的时候可以直接参考这三个大项目[Uniswap前端源码](https://github.com/Uniswap/interface "Uniswap前端源码")，[Sushiswap前端源码](https://github.com/sushiswap/sushiswap-interface "Sushiswap前端源码")，[PancakeSwap前端源码](https://github.com/pancakeswap/pancake-frontendl "PancakeSwap前端源码")。
2.[Vite版本](https://github.com/Verin1005/React-Vite-Dapp-Template "Vite版本")。之前使用了CRA构建了一套，但是和很多Dapp开发的库不兼容，后来尝试了Vite，不仅速度快而且很多基本完美兼容，所以构建了一套基础逻辑和Nextjs一样为SPA页面服务的模板。

# 所使用的技术栈

两个框架技术栈主要是：TypeScript+React17+ethers+web3-react;

备注：这些技术栈也是根据其他项目使用情况来决定的，虽然有wagmi的web3 hooks库，可是由于没有其他项目使用，所以暂时未考虑࿰

