---
layout:     post
title:      【Java | ETH】web3j 调用智能合约有两种方式
subtitle:   【Java | ETH】web3j 调用智能合约有两种方式
date:       2019-08-14 18:10:08
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - ETH
    - 区块链
    - 区块链零基础到实战教程
    - 以太坊
    - java
    - 智能合约
---

>【Java | ETH】web3j 调用智能合约有两种方式

# 【Java | ETH】web3j 调用智能合约有两种方式


### **1****、第一种：直接使用****RawTrasaction****进行创建**

// using a raw transaction RawTransaction rawTransaction = RawTransaction.createContractTransaction( <nonce>, <gasPrice>, <gasLimit>, <value>, "0x <compiled smart contract code>"); // send... // get contract address EthGetTransactionReceipt transactionReceipt = web3j.ethGetTransactionReceipt(transactionHash).send(); if (transactionReceipt.getTransactionReceipt.isPresent()) { String contractAddress = transactionReceipt.get().getContractAddress(); } else { // try again }

### **2、第二种：将合约代码转换成****Java Bean****。**

（1）首先我们需要一份写好的智能合约。
pragma solidity ^0.4.18; // Example taken from http
