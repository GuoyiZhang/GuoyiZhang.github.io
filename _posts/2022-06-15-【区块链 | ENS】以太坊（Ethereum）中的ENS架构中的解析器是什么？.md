---
layout:     post
title:      【区块链 | ENS】以太坊（Ethereum）中的ENS架构中的解析器是什么？
subtitle:   【区块链 | ENS】以太坊（Ethereum）中的ENS架构中的解析器是什么？
date:       2022-06-15 14:01:05
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - ENS技术架构指南【系统分析】
    - 区块链
    - 以太坊
    - p2p
---

>【区块链 | ENS】以太坊（Ethereum）中的ENS架构中的解析器是什么？

# 【区块链 | ENS】以太坊（Ethereum）中的ENS架构中的解析器是什么？


# ENS 公共解析器

[源代码](https://github.com/ensdomains/resolvers/blob/master/contracts/PublicResolver.sol "源代码")

公共解析器是一个通用的 ENS 解析器，它适用于大多数标准的 ENS 用例。名称的所有者可以使用公共解析器更新相应的 ENS 记录。

公共解析器遵循以下 EIP 提案:

* [EIP137](https://eips.ethereum.org/EIPS/eip-137 "EIP137") - Contract address interface ( 

addr()
 ).
* [EIP165](https://eips.ethereum.org/EIPS/eip-165 "EIP165")- Interface Detection ( 

supportsInterface()
 ).
* [EIP181](https://eips.ethereum.org/EIPS/eip-181 "EIP181") - Reverse resolution ( 

name()
 ).
* [EIP205](https://eips.ethereum.org/EIPS/eip-205 "EIP205") - ABI support ( 

ABI()
 ).
* [EIP619](https://github.com/ethereum/EIPs/pull/619 "EIP619") - SECP256k1 public keys ( 

pubkey()
 ).
* [EIP634](https://eips.ethereum.org/EIPS/eip-634 "EIP634") - Te

