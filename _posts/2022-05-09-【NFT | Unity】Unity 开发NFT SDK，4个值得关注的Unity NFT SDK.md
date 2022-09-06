---
layout:     post
title:      【NFT | Unity】Unity 开发NFT SDK，4个值得关注的Unity NFT SDK
subtitle:   【NFT | Unity】Unity 开发NFT SDK，4个值得关注的Unity NFT SDK
date:       2022-05-09 10:50:59
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 区块链零基础到实战教程
    - 区块链
    - unity
    - 游戏引擎
    - nft
    - 区块链
---

>【NFT | Unity】Unity 开发NFT SDK，4个值得关注的Unity NFT SDK

# 【NFT | Unity】Unity 开发NFT SDK，4个值得关注的Unity NFT SDK


在游戏中集成区块链NFT支持已经成为2022年不可忽视的趋势。本文将介绍最新的4个 适合Unity游戏开发者的NFT SDK。

![](https://img-blog.csdnimg.cn/img_convert/40a3d9d412dd0fe23c591983c7b976c3.png)

## 1、ChainSafe Gaming SDK

ChainSafe Gaming SDK的目的是帮助Unity开发者提供接入以太坊系列区块链并创建游戏NFT。

![](https://img-blog.csdnimg.cn/img_convert/75547cf5c1c62c12071dc8aaf353567b.png)

Chainsafe Gaming SDK内置ERC20、ERC721和ERC1155的访问能力，例如查看指定地址持有的 全部NFT：
1 2 3 4 5 6 string chain = "ethereum"; string network = "rinkeby"; // mainnet ropsten kovan rinkeby goerli string account = "0xebc0e6232fb9d494060acf580105108444f7c696"; string contract = ""; string response = await EVM.AllErc721(chain, network, account, contract); print(response);

ChainSafe Gaming SDK目前支持的区块链包括&/#x


