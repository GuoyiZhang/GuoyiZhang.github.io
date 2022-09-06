---
layout:     post
title:      【区块链 | ENS】ENS如何接入DNS？ENS智能合约如何验证DNS所有权？DNS注册器介绍？
subtitle:   【区块链 | ENS】ENS如何接入DNS？ENS智能合约如何验证DNS所有权？DNS注册器介绍？
date:       2022-06-21 17:16:54
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - ENS技术架构指南【系统分析】
    - 区块链
    - 智能合约
---

>【区块链 | ENS】ENS如何接入DNS？ENS智能合约如何验证DNS所有权？DNS注册器介绍？

# 【区块链 | ENS】ENS如何接入DNS？ENS智能合约如何验证DNS所有权？DNS注册器介绍？


在 ENS 上，有两个关于 DNS 的智能合约，[DNSSECOracle](https://github.com/ensdomains/dnssec-oracle "DNSSECOracle") 和 [DNSRegistrar](https://github.com/ensdomains/dnsregistrar "DNSRegistrar")。

DNSSEC（名称系统安全扩展）建立了一个信任链，从 ICANN 签名的根密钥（.）向下至每个密钥。我们首先知道 DNS 根密钥的哈希值（这是在 oracle 智能合约中硬编码的）。有了该密钥的哈希值，就可以传入实际的密钥，验证它是否与此哈希值匹配，验证后我们可以将它添加到可信记录集。

有了该密钥，我们现在可以验证任何用该密钥签名的记录，在本例中，它是 xyz 顶级域的根哈希值。这样，我们就能识别密钥，等等。

![](https://img-blog.csdnimg.cn/img_convert/f4c322a3996f62b54cddac7a980fe08d.png)

任何人都可以在以太坊区块链上向 DNSSEC oracle 提交经过 DNSSEC 签名的 DNS 记录的证明，只要是采用当前支持的公钥方案和摘要签名的就行。如果一个人能够通过 DNSSEC Oracle 证明他拥有某个 DNS 域名的所有权，那么 DNSRegistrar 会将相应的 ENS 名称分配给他。

## DNSRegistrar 的地址

* Mainnet: 待定
* Ropsten: 0x475e527d54b91b0b011DA573C69Ac54B2eC269ea

