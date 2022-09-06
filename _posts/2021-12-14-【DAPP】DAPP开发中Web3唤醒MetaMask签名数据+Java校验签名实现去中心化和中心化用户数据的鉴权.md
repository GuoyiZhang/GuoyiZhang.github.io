---
layout:     post
title:      【DAPP】DAPP开发中Web3唤醒MetaMask签名数据+Java校验签名实现去中心化和中心化用户数据的鉴权
subtitle:   【DAPP】DAPP开发中Web3唤醒MetaMask签名数据+Java校验签名实现去中心化和中心化用户数据的鉴权
date:       2021-12-14 19:20:09
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 区块链零基础到实战教程
    - 区块链
    - Java
    - java
    - 去中心化
    - 开发语言
---

>【DAPP】DAPP开发中Web3唤醒MetaMask签名数据+Java校验签名实现去中心化和中心化用户数据的鉴权

# 【DAPP】DAPP开发中Web3唤醒MetaMask签名数据+Java校验签名实现去中心化和中心化用户数据的鉴权

使用场景大多数用在DAPP中调用中心化数据或者操作某些中心化功能的时候通过DAPP调用MetaMask钱包对数据进行签名传递给后台，后台验证签名数据是否是否当前用户钱包地址签名的数据实现鉴权。

# 一、DAPP端用Web3签名数据

注：不同的web3版本签名代码有点差异

1、0.26版本签名 web3.personal.sign
//参数1：要签名的数据//参数2：签名的钱包地址web3.personal.sign(web3.fromUtf8("Hello Dapp"), "0x40141cF4756A72DF8D8f81c1E0c2ad403C127b9E",(err, res) => {          console.log("签名后的数据：",res)          //0x53ea88d24f4ef8cdcc4bcc843912510b065cd6014c453ff61316c4cd75162f0a38f83a2103da028fb8e5181292ba194b0c8aa21a9ddacdf6783ebfa608889d121c      }) //web3.eth.sign此签名方法MetaMask会提示未来版本会被移除

2、1.0版本签名  web3.eth.personal.sign

3、唤醒MetaMask钱包签名数据
签名后数据为：0x53ea88d24f4ef8cdcc4bcc843912510b065cd6014c453ff61316c4cd75162f0a38f83a2103da028fb8e5181292ba194b0c8aa21a9ddacdf6783ebfa608889d121c


