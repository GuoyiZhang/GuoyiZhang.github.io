---
layout:     post
title:      【智能合约】js解析智能合约Solidity返回的struct
subtitle:   【智能合约】js解析智能合约Solidity返回的struct
date:       2021-12-14 18:36:20
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 区块链
    - Solidity零基础学习教程
    - 区块链零基础到实战教程
    - 智能合约
    - javascript
    - 区块链
    - solidity
---

>【智能合约】js解析智能合约Solidity返回的struct

# 【智能合约】js解析智能合约Solidity返回的struct


## 前言

Solidity是以太坊智能合约的编程语言，我们可以通过web3.js来与合约进行通信，并接收Solidity函数的返回值。不少人在接收struct类型的返回值时不知道怎么处理，本文展示一种解析方法，以供各位学习交流，如有更好的方法，欢迎讨论。

# Solidity返回的struct

首先看solidity代码，截取了关键部分，定义了一个结构体以及一个id到结构体的映射，该映射为public，意味着会自动生成getter函数。
struct Status{ string studentId; // 学籍号 string schoolId;// 学校 string majorId;// 专业 uint8 length;// 学制 uint8 eduType;// 学历类别 uint8 eduForm;// 学习形式 uint8 level;// 层次(专/本/硕/博) uint8 state;// 学籍状态 uint16 class;// 班级 uint32 admissionDate;// 入学日期 uint32 departureDate;// 离校日期 } mapping (uint32=>Status) public idToUndergraduate;// id到本科学籍的映射

# js调用合约

this.StudentFactory = TruffleContract(StudentFactoryABI); if (typeof web3 !== 'undefined&
