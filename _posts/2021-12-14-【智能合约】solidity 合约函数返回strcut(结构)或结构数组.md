---
layout:     post
title:      【智能合约】solidity 合约函数返回strcut(结构)或结构数组
subtitle:   【智能合约】solidity 合约函数返回strcut(结构)或结构数组
date:       2021-12-14 18:31:57
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 区块链
    - Solidity零基础学习教程
    - 区块链零基础到实战教程
    - 智能合约
    - 以太坊
    - 区块链
---

>【智能合约】solidity 合约函数返回strcut(结构)或结构数组

# 【智能合约】solidity 合约函数返回strcut(结构)或结构数组


## 前言

在solidity 0.4 时代，是不支持返回struct的。但现在solidity已经进入了0.8的版本，这个版本是支持直接返回struct与struct array的，以下为具体做法。

## []()[]()代码示例

// SPDX-License-Identifier: GPL-3.0 pragma solidity >=0.7.0 <0.9.0; contract ContractFactory { struct User { uint256 id; uint256 age; string name; } mapping(uint256 => User) public users; mapping(uint256 => User[]) public userGroup; function init() public { User memory newUser1 = User({ id: 1, age: 24, name: "lily" }); User memory newUser2 = User({ id: 2, age: 25, name: "brain" }); userGroup[1].push(newUser1); userGroup[1].push(newUser2); User storage newUser = users[1]; newUser.age = 16; newUser
