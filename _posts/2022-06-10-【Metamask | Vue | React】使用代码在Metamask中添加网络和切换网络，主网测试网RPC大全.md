---
layout:     post
title:      【Metamask | Vue | React】使用代码在Metamask中添加网络和切换网络，主网测试网RPC大全
subtitle:   【Metamask | Vue | React】使用代码在Metamask中添加网络和切换网络，主网测试网RPC大全
date:       2022-06-10 10:51:45
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 区块链零基础到实战教程
    - 区块链
    - vue.js
    - 前端
    - javascript
---

>【Metamask | Vue | React】使用代码在Metamask中添加网络和切换网络，主网测试网RPC大全

# 【Metamask | Vue | React】使用代码在Metamask中添加网络和切换网络，主网测试网RPC大全

在Dapp中，自动给用户Metamask添加一个网络（比如Heco主网、Heco测试网、BSC、MATIC、FTM、OKExchain网络），程序主动切换用户Metamask的网络为指定网络（比如Heco主网）

## 1. 使用AddEthereumChainParameter API

Metamask新增了一个AddEthereumChainParameter 的api，可以自动给用户Metamask添加一个网络，并把当前页面连接到这个网络。

执行这个api时，如果用户的Metamask还没有添加参数指定的网络，则Metamask会弹出一个提示框，提示用户当前页面要给Metamask添加一个网络，并显示要添加网络的信息。如果用户点确定，Metamask就会添加这个网络到用户的钱包。

如果用户的Metamask已经添加了参数指定的网络，但是当前钱包连接的网络不是这个网络，则Metamask会弹出一个提示框，提示用户当前页面要把Metamask的网络切换到指定网络，如果用户点确定，Metamask当前连接的网络就会切换到指定网络。

以上判断Metamask是否添加了指定网络，是根据参数中的ChainID来判断，网络id一样就认为是同一个网络。

如果执行时Metamask已经添加了指定网络，当前也正好连接的是这个网络，则什么也不会执行，不会有任何提示。

在官方文档中，说这个api必须由用户主动操作来触发（点某个按钮），但是实测并不需要，在页面加载时，程序自动执行这个api也是可以的。所以可以在应用初始化的地方，固定执行这个api，这样当用户没有添加网络时，会

