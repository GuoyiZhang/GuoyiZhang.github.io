---
layout:     post
title:      【DAPP】开发DAPP使用Web3+Vue唤醒MetaMask钱包和合约交互
subtitle:   【DAPP】开发DAPP使用Web3+Vue唤醒MetaMask钱包和合约交互
date:       2021-12-14 19:13:05
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 前端
    - Solidity零基础学习教程
    - 区块链零基础到实战教程
    - 以太坊
    - vue.js
    - 交互
---

>【DAPP】开发DAPP使用Web3+Vue唤醒MetaMask钱包和合约交互

# 【DAPP】开发DAPP使用Web3+Vue唤醒MetaMask钱包和合约交互

注： 1、we3版本：0.20.7 2、下面使用的是标准的ERC20合约测试 3、其他 HECO BSC OK Polygon 公链对接类似

![watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBASVRf5rWp5ZOl,size_20,color_FFFFFF,t_70,g_se,x_16](https://img-blog.csdnimg.cn/20210911163209425.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBASVRf5rWp5ZOl,size_20,color_FFFFFF,t_70,g_se,x_16)

1、在 mounted 中自动检测浏览器是否安装MetaMask钱包
mounted () {     if (window.ethereum) {       window.ethereum.enable().then((res) => {         //alert("当前钱包地址："+res[0])       })     }else{       alert("请安装MetaMask钱包")     }   }

2、查询钱包余额

let web3 = new Web3(window.web3.currentProvider) let fromAddress = web3.eth.accounts[0]; web3.eth.getBalance(fromAddress,(err, res) => { if (!err) { console.log("ETH余额==",res.toNumber()/Math.pow(10,18)); } })

3、查询token余额

let web3 = new Web3(window.web3.currentProvider) let fromAddress = web3.eth.accounts[0]; let ethContract = web3.eth.contract("token合约Abi") let functionInstance = ethContract.at("token合约地址") functionInstance.balanceOf(fromAddress,(err, res) => { if (!err) { //代币精度根据所发行的token合约精度换算 console.log("token余额==",res.toNumber()/Math.pow(10,18)); } })

4、ETH转账

let web3 = new Web3(window.web3.currentProvider) let fromAddress = web3.eth.accounts[0]; //转账数量 let amount = 1/*Math.pow(10,18); //收款地址 let toAddress = "0x40141cF4756A72DF8D8f81c1E0c2ad403C127b9E"; web3.eth.sendTransaction({ gas: 21000, gasPrice: 5000000000, from: fromAddress, to: toAddress, value: amount }, (err, result) => { console.log("转账Hash=",result) })

5、token转账

let web3 = new Web3(window.web3.currentProvider) let ethContract = web3.eth.contract("tokenAbi") let functionInstance = ethContract.at("token地址") //当前钱包地址 let fromAddress = web3.eth.accounts[0]; //转账数量 let amount = 100/*Math.pow(10,18); //接收地址 let toAddress = "0x40141cF4756A72DF8D8f81c1E0c2ad403C127b9E"; functionInstance.transfer(toAddress,amount,{ gas: 21000, gasPrice: 5000000000, from: fromAddress }, (err, result) => { console.log("转账Hash:",result) })

 6、token授权

let web3 = new Web3(window.web3.currentProvider) let abiContract = web3.eth.contract("token合约Abi") let contractInstance = abiContract.at("token合约地址") //当前钱包地址 var fromAddress = web3.eth.accounts[0]; //授权数量 var amount = 1000 /* Math.pow(10,18); contractInstance.approve("授权地址",amount,{ gas: 80000, gasPrice: 1500000000, from: fromAddress }, (err, result) => { console.log("交易Hash：",result) })

7、查询授权数量

let web3 = new Web3(window.web3.currentProvider) let abiContract = web3.eth.contract("代币合约Abi") let contractInstance = abiContract.at("代币合约地址") contractInstance.allowance("授权的用户钱包地址","授权的地址",(err, res) => { if (!err) { console.log("授权数量==",res.toNumber()); } })

8、查询去中心化交易所LP地址数量 计算价格

let web3 = new Web3(window.web3.currentProvider) let ethContract = web3.eth.contract("LP合约Abi") let functionInstance = ethContract.at("LP合约地址") functionInstance.getReserves((err, res) => { if (!err) { // 代币数量1 res[0].toNumber() // 代币数量2 res[1].toNumber() //根据先后顺序 2个相除可以得到价格 } })

 

## 有任何疑问添加

## vx：blocksvc

## qq：528930672 

 

