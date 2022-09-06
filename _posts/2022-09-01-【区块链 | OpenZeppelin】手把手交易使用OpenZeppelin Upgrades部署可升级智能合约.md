---
layout:     post
title:      【区块链 | OpenZeppelin】手把手交易使用OpenZeppelin Upgrades部署可升级智能合约
subtitle:   【区块链 | OpenZeppelin】手把手交易使用OpenZeppelin Upgrades部署可升级智能合约
date:       2022-09-01 17:50:53
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 区块链零基础到实战教程
    - OpenZeppelin
    - 区块链
    - 智能合约
---

>【区块链 | OpenZeppelin】手把手交易使用OpenZeppelin Upgrades部署可升级智能合约

# 【区块链 | OpenZeppelin】手把手交易使用OpenZeppelin Upgrades部署可升级智能合约


如何部署以太 坊可升级智能合约

## 为什么要升级合约？

根据设计，智能合约是不可变的。另一方面，软件质量在很大程度上取决于升级和修补源代码以生成迭代版本的能力。尽管基于区块链的软件从技术的不变性中获益匪浅，但修复错误和潜在的产品改进仍然需要一定程度的可变性。OpenZeppelin Upgrades 通过为智能合约提供易于使用、简单、健壮和可选的升级机制来解决这一明显的矛盾，该机制可以由任何类型的治理控制，无论是多重签名钱包、简单地址还是复杂的 DAO。

----

## 首次部署

需要部署三个合约，分别是逻辑合约，代理管理合约，代理合约。逻辑合约就是我们自己的业务合约，需要满足OpenZeppelin可升级合约的条件。以下业务合约以逻辑合约为例进行说明。
本文使用remix部署合约，如需快速部署请参考：[【翻译】用 Hardhat 进行升级部署（Using with Hardhat） | 登链社区](https://learnblockchain.cn/article/2908 "【翻译】用 Hardhat 进行升级部署（Using with Hardhat） | 登链社区")

### 第一步，逻辑合约

首先部署逻辑合约。
// SPDX-License-Identifier: MIT pragma solidity ^0.8.0; import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol"; import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradea
