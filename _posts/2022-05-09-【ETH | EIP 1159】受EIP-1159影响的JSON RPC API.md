---
layout:     post
title:      【ETH | EIP 1159】受EIP-1159影响的JSON RPC API
subtitle:   【ETH | EIP 1159】受EIP-1159影响的JSON RPC API
date:       2022-05-09 11:17:58
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 区块链零基础到实战教程
    - ETH
    - 区块链
    - 以太坊
    - rpc
    - 数字货币
    - EIP 1159
---

>【ETH | EIP 1159】受EIP-1159影响的JSON RPC API

# 【ETH | EIP 1159】受EIP-1159影响的JSON RPC API

EIP-1159升级了以太坊的交易定价机制，将gasPrice分为base和tip两部分。EIP-1159 不能兼容之前的版本，因此将导致硬分叉。包含EIP-1159升级的分叉被称为伦敦分叉， 大约在8月4日发生。在这篇文章中，我们将介绍EIP-1159造成的以太坊JSON RPC API变化。

[eth1.0-apis仓库](https://github.com/ethereum/eth1.0-apis "eth1.0-apis仓库") 没有版本号，因此很难跟踪 EIP-1159引发的JSON RPC API变化。下面是我们找出的API变化清单。

EIP-1559引入了一种新的交易类型（0x02）并在区块头加入一个新的字段（baseFeePerGas）。 总体来说，任何返回交易或区块的RPC API都会在EIP-1159生效后受到影响。

下面的API调用受到EIP-1159的影响，标记

/*
的表明该API及其变化形式都受到影响：

## eth_call

eth_call涉及到显著的修改，具体描述参见[这里](https://github.com/ethereum/go-ethereum/pull/23027 "这里")。

## eth_getBlockBy/*

在伦敦分叉后的区块中会增加一个新的字段 

baseFeePerGas
 。

## eth_getRawTransaction/*

在伦敦分叉后可能反馈RPL编码的EIP-1159交易。

## eth_getTransactionBy/*


