---
layout:     post
title:      【区块链 | Solana】Solana链上程序开发入门【源码】
subtitle:   【区块链 | Solana】Solana链上程序开发入门【源码】
date:       2022-05-09 11:14:05
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 区块链零基础到实战教程
    - 区块链
    - 区块链
    - solana
    - web3
---

>【区块链 | Solana】Solana链上程序开发入门【源码】

# 【区块链 | Solana】Solana链上程序开发入门【源码】


在这个教程里，我们将学习如何开发Solana链上程序，内容包括创建Solana账号、 从测试链获取免费的SOL、编译部署与测试流程，并开发一个简单的Solana链上程序。 在教程结束部分提供了完整源码的下载链接。

[]()
![](https://img-blog.csdnimg.cn/img_convert/f47b13b7439ef044e0533a656705aad3.png)

 

在深入学习本教程之前，请确保已按照[这个教程](https://solongwallet.medium.com/solana-development-tutorial-environment-setup-2649cb81305 "这个教程")中的步骤设置了环境并安装了工具套件。 可以访问这里查看[Solana RPC API文档](http://cw.hubwiz.com/card/c/solana-rpc-api/ "Solana RPC API文档")。

## 1、连接到Solana开发网

如果你没有自己的节点也不要担心，Solana提供了与主网相同配置的devnet。所以在这里让我们首先将 API 端点 设置为开发链 - https://devnet.solana.com：
1 2 3 4 ubuntu@VM-0-12-ubuntu:~$ solana config set --url https://devnet.solana.com Config File: /home/ubuntu/.config/solana/cli/config.yml RPC URL: https://devnet.solana.com WebSocket URL: wss://devn
