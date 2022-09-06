---
layout:     post
title:      【区块链 | NFT】NFT必修课：如何使用IPFS创建NFT以及部署智能合约？
subtitle:   【区块链 | NFT】NFT必修课：如何使用IPFS创建NFT以及部署智能合约？
date:       2022-04-04 00:02:32
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - ETH
    - NFT通俗易懂教程
    - 区块链零基础到实战教程
    - 去中心化
    - 以太坊
    - 区块链
    - ipfs
    - nft
---

>【区块链 | NFT】NFT必修课：如何使用IPFS创建NFT以及部署智能合约？

# 【区块链 | NFT】NFT必修课：如何使用IPFS创建NFT以及部署智能合约？


**目录**

[使用 IPFS 铸造、存储 NFT Toekn](#%E4%BD%BF%E7%94%A8%20IPFS%20%E9%93%B8%E9%80%A0%E3%80%81%E5%AD%98%E5%82%A8%20NFT%20%E8%B5%84%E4%BA%A7)

[铸造NFT](#%E9%93%B8%E9%80%A0NFT)

[步骤 0 — Toekn的所有权](#%E6%AD%A5%E9%AA%A4%200%20%E2%80%94%20%E8%B5%84%E4%BA%A7%E7%9A%84%E6%89%80%E6%9C%89%E6%9D%83)

[步骤 1 — 准备Toekn](#%E6%AD%A5%E9%AA%A4%201%20%E2%80%94%20%E5%87%86%E5%A4%87%E8%B5%84%E4%BA%A7)

[第 2 步 — 选择市场并进行身份验证](#%E7%AC%AC%202%20%E6%AD%A5%20%E2%80%94%20%E9%80%89%E6%8B%A9%E5%B8%82%E5%9C%BA%E5%B9%B6%E8%BF%9B%E8%A1%8C%E8%BA%AB%E4%BB%BD%E9%AA%8C%E8%AF%81)

[第 3 步 — 通过上传文件开始创建 NFT](#%E7%AC%AC%203%20%E6%AD%A5%20%E2%80%94%20%E9%80%9A%E8%BF%87%E4%B8%8A%E4%BC%A0%E6%96%87%E4%BB%B6%E5%BC%80%E5%A7%8B%E5%88%9B%E5%BB%BA%20NFT)

[步骤 4 — 创建 IPFS 链接](#%E6%AD%A5%E9%AA%A4%204%20%E2%80%94%20%E5%88%9B%E5%BB%BA%20IPFS%20%E9%93%BE%E6%8E%A5)

[步骤 5 — NFT 属性](#%E6%AD%A5%E9%AA%A4%205%20%E2%80%94%20NFT%20%E5%B1%9E%E6%80%A7)

[步骤 — 6 出售 NFT](#%E6%AD%A5%E9%AA%A4%20%E2%80%94%206%20%E5%87%BA%E5%94%AE%20NFT)

[使用 IPFS 创建 NFT 的智能合约](#%E4%BD%BF%E7%94%A8%20IPFS%20%E5%88%9B%E5%BB%BA%20NFT%20%E7%9A%84%E6%99%BA%E8%83%BD%E5%90%88%E7%BA%A6)

----

多年来，数字艺术并未被认为是“真正的”艺术。绘画、雕塑和装置是“真正的”艺术，而数字艺术被视为“二流”艺术。然而，数字艺术家也花费了大量时间来建立他们的艺术和完善他们的技能，就像更多的“古典”艺术家一样。

多年来，数字艺术和数字艺术家的作品并没有得到应有的报酬。数字艺术家更像是自由职业者，从一场演出到另一场演出，总是很难从他们的数字艺术中赚到更多的钱。

原因是很难让数字艺术独一无二。数字艺术一旦被创造出来，就很容易在互联网上被复制数千次，而且很难追踪这些副本并区分哪一个是原始的。

在古典绘画中，我知道萨尔瓦多·达利（Salvador Dalli）的《记忆的永恒》上有成千上万张照片，但原始实物绘画是某人所有的（在这种情况下，它在纽约现代艺术博物馆）。

![](https://img-blog.csdnimg.cn/img_convert/caee88b7ede3b368229a080ea10c09e5.png)

尽管我可以多次复制/粘贴此图像，但原始图像始终具有价值，因为很容易证明原始图像的所有权。原件被物理锁在博物馆里

我们如何将数字艺术变成独一无二的东西？

那便是今天的主角——NFT。

NFT 是具有不可替代性的Toekn，这意味着每个NFT都是独一无二且不可替代的。他们通常使用以太坊 ERC-721标准，该标准于 2018 年 1 月在以太坊网络中引入并彻底改变了整个行业。

如果你想了解如何创建和铸造数字 NFT，让我们开始吧！

## 使用 IPFS 铸造、存储 NFT Toekn

让我们将 NFT 创建分解为两个部分。首先，有处理 NFT 的铸造和存储的区块链。区块链通过在全球数千台计算机/节点上复制它，来确保 NFT 的元数据是不可变的和安全的。然而，区块链无法处理存储大量数据，因为在这数千个节点之间复制大量数据变得极其昂贵。这就是第二部分：存储 NFT 数据。

在以太坊区块链上存储图像可能会花费数万美元。出于这个原因，大多数 NFT 数据需要存储在链外，我们也需要保护这些数据。

我们可以通过 IPFS——星际文件系统，一种用于共享和存储文件的点对点协议来解决这个问题。IPFS 使用内容寻址来唯一标识全局命名空间中的每个文件，这对于我们的 NFT 将 NFT 元数据链接到Toekn或艺术品的存储位置很重要。与 Dropbox 或 Google Drive 等集中式服务相比，IPFS 可以被视为具有数据固定的更持久性。

创建 NFT 时，我们需要使用引用Toekn的 URL 链接。此 URL 将包含在 NFT 的元数据中。正如您现在所知，NFT 数据是不可变的，它将永远存在于区块链中，因此为与 NFT 相关的Toekn或图像找到一个合适的家也很重要。

![](https://img-blog.csdnimg.cn/img_convert/5f4a7741a6e13bb749976047f716d3dc.png)

*Pinata 是著名的 IPFS 服务之一：pinata.cloud*

IPFS 使用称为 CID 的内容标识符，它将内容作为哈希引用。这些 CID 是 URL 的一部分，如果内容没有改变，URL 也不会改变。某个 CID 和相应 URL 后面的图像将始终是相同的图像，这使我们对链下存储的 NFT 数据具有一定程度的不变性。

在“逐步铸造”部分中，我们将看到如何使用 Pinata 创建 IFPS CID/URL 并将其与我们将要铸造的 NFT 相关联。

## **铸造NFT**

### 步骤 0 — Toekn的所有权

在创建 NFT 之前，您需要确保您是Toekn/艺术品的创建者或所有者。你必须有办法证明你是所有者或创造者。

### 步骤 1 — 准备Toekn

确保您拥有该图像的文件。您可以简单地对 JPEG/PNG 进行标记，但最好也有源文件或高质量的文件。如果您处理的是数字艺术，TIFF、AI/EPS 也可以在销售过程中共享。

### 第 2 步 — 选择市场并进行身份验证

现在我们需要铸造 NFT 。当您想出售NFT时，可以直接在 OpenSea 市场上铸造它，或者您可以先在 Rarible 上铸造它，因为在 Rarible 上，您可以铸造NFT而无需实际出售它。由你来决定。

在这一步一步中，我假设你已经安装了 Metamask 浏览器插件，并且有一些 ETH 用于手续费。

在 OpenSea 上，单击创建并连接您的 Metamask 钱包（检查钱包部分）。单击 Metamask 图标登录到您的 Metamask 钱包，然后单击连接。之后你还需要 Ether 在铸造过程中向网络支付交易费用，但现在你不需要花钱。

连接您的钱包后，您将使用您的公钥在网站上进行身份验证和识别。这类似于您使用 Google 或 Facebook 身份登录（也称为 SAML/SSO — 单点登录）。

### 第 3 步 — 通过上传文件开始创建 NFT

要创建新项目，请继续并单击创建。您必须创建一个集合，并且您的 NFT 可以成为集合的一部分。以后可以制作更多的收藏品——例如，2D 收藏品、3D 收藏品等。

创建集合后，您可以向集合中“添加新项目”。点击“添加新项目”。您将能够上传文件，并且您会发现多种可用格式：PNG、GIF、WEBP、MP4、MP3 等等。您可以在此处选择并上传您的文件。

### 步骤 4 — 创建 IPFS 链接

重要的是要强调图像本身并没有存储在区块链上。存储在区块链上的只是关于图像的元数据，即文件的哈希值、名称、时间戳和指向文件存储位置的链接。区块链不适合存储大文件，而且文件总是需要存储在其他地方。对于 OpenSea，他们将负责存储图像。

如果您希望买家收到高分辨率文件或源文件，您也可以将此文件存储在存储服务（IPFS、Google Drive、S3 或 Dropbox）中，并在“可解锁内容”字段中共享文件链接. 购买完成后，此文件将与买家共享。

为了让事情更加去中心化并保持区块链精神，我们不要使用像 Google Drive 或 Dropbox 这样的集中式存储服务，而是使用 IPFS——星际文件系统。IPFS 不是区块链，而是一个分布式点对点文件系统（类似于 BitTorrent），允许我们存储和共享文件。

使用 UPFS 的最简单方法是 Pinata。如果您尚未注册，请转到 Pinata.cloud 并注册。拥有 Pinata 帐户后，转到仪表板，然后单击上传。选择文件并上传。

文件上传后，您将找到一个 CID 哈希（内容标识符），类似于 Qma4Jse7V6tZ7k3756iPv39tsMG6DhxUQrc42cKoAVVsbR。

这是将链接到图像的哈希值。同时复制图像的链接，返回 OpenSea 网站，并将其粘贴到“可解锁内容”字段中。该链接应如下所示：

[https://gateway.pinata.cloud/ipfs/Qma4Jse7V6tZ7k3756iPv39tsMG6DhxUQrc42cKoAVVsbR](https://gateway.pinata.cloud/ipfs/Qma4Jse7V6tZ7k3756iPv39tsMG6DhxUQrc42cKoAVVsbR "https://gateway.pinata.cloud/ipfs/Qma4Jse7V6tZ7k3756iPv39tsMG6DhxUQrc42cKoAVVsbR")

### 步骤 5 — NFT 属性

完成附加属性和标签。

最后点击创建。

您现在已经在 OpenSea 网站上创建了Toekn，但它仍未上市出售。

### 步骤 — 6 出售 NFT

转到您的商品页面，然后单击“出售”。

您还可以设置“设置价格”。这类似于 Ebay 的“立即购买”，它是您愿意立即出售您的商品的价格。价格可以用不同的Toekn列出，但最常见的是ETH。

您也可以选择“最高出价”。这是拍卖选项，在此选项中，您可以选择最低出价、底价和拍卖截止日期。

最后，点击“发布您的列表”。

单击后，按照步骤铸造令牌。您的 Metamask 窗口将提示（如果没有，您需要单击 Metamask 图标）并单击符号。OpenSea 不收取任何费用，但是每当您创建新的 NFT 时，您都会将数据写入区块链，并且您将产生 gas 费用（即以太坊网络的费用）。

单击“批准”后，它会提示您的 Metamask 钱包，以便您支付费用。在您的 Metamask 钱包上，您可以单击“编辑”来编辑费用并选择慢速或快速。慢意味着您将支付更少的gas费用，但交易可能需要更长的时间才能在区块链中结算（通常不到1小时）。

考虑到以太坊可能会拥堵，铸造新 NFT 的成本有时可能会很高，但未来可能会降低。

这样你的 NFT 现已上市，人们将能够竞标或购买。

## 使用 IPFS 创建 NFT 的智能合约

如果你对代码非常感兴趣并想部署自己的 ERC-721 智能合约，那么你需要完成以下几个重要步骤：

1、获取一些测试ETH（教学将在Ropsten测试网）

2、下载 IPFS

3、将你的作品上传到 IPFS

4、打开 Ethereum Remix 并创建智能合约

5、部署智能合约

6、铸造 NTF

**获取ETH测试Toekn**

首先，使用 Metamask（小狐狸钱包），将你的钱包网络切换到 Ropsten 测试网。

然后打开 Ropsten 水龙头网站： [Ropsten Faucet is EOL](https://faucet.ropsten.be/ "Ropsten Faucet is EOL")  ，将你的钱包地址复制到水龙头并获取一些测试Eth **Toekn**。我们将需要它来支付智能合约的gas费用。

![](https://img-blog.csdnimg.cn/img_convert/42180299e504aca208d5062c5cfd7a28.png)

**下载 IPFS 并上传您的艺术作品文件**

大多数 NFT 数据需要存储在链外，我们需要保护这些数据。

我们可以通过 IPFS——星际文件系统，一种用于共享和存储文件的点对点协议来解决这个问题。IPFS 使用内容寻址来唯一标识全局命名空间中的每个文件，这对于我们的 NFT 将 NFT 元数据链接到Toekn或艺术品的存储位置很重要。因此，与 Dropbox 或 Google Drive 等集中式服务相比，IPFS 可以被视为具有数据固定的更持久性。

我们将使用 IPFS 来存储我们的 NFT 文件。前往 IPFS 网站并在您的台式机/笔记本电脑上安装 IPFS。安装后，运行它。恭喜，您现在是一个 IPFS 节点！

IPFS 官网：[https://docs.ipfs.io/install/ipfs-desktop//#windows](https://docs.ipfs.io/install/ipfs-desktop/#windows "https://docs.ipfs.io/install/ipfs-desktop/#windows")

![](https://img-blog.csdnimg.cn/img_convert/44eeb006cc1c30562cc1b38ea182f3a9.png)

单击文件并上传您的艺术品！

上传后，您将可以访问可共享的链接，将链接复制保存下来。

![](https://img-blog.csdnimg.cn/img_convert/0fcb4c92331490fbed3ce21cab15c8f0.png)

**打开 Ethereum Remix 并创建智能合约**

现在，我们转到 Ethereum Remix IDE 并创建一个新的 Solidity 文件，例如“erc721.sol”。我们将使用 Ethereum Remix 并使用0xcert/ethereum-erc721合约来创建我们的 NFT 智能合约。

（Ethereum Remix 是一个开源 Web 应用程序，允许您开发、编译和部署智能合约。）

![](https://img-blog.csdnimg.cn/img_convert/3cad2d0ca02571a9e19eabd0855dd8ec.png)

将以下脚本复制/粘贴到新创建的 .sol 文件中：
// SPDX-License-Identifier: MIT
pragma solidity 0.8.6;
导入“  [https://github.com/0xcert/ethereum-erc721/src/contracts/tokens/nf-token-metadata.sol](https://github.com/0xcert/ethereum-erc721/src/contracts/tokens/nf-token-metadata.sol "https://github.com/0xcert/ethereum-erc721/src/contracts/tokens/nf-token-metadata.sol") ”；
导入“  [https://github.com/0xcert/ethereum-erc721/src/contracts/ownership/ownable.sol](https://github.com/0xcert/ethereum-erc721/src/contracts/ownership/ownable.sol "https://github.com/0xcert/ethereum-erc721/src/contracts/ownership/ownable.sol") ”；
合约 newNFT 是 NFTokenMetadata, Ownable {
 constructor() {
   nftName = "Synth NFT";
   nftSymbol = "SYN";
 }
 function mint(address _to, uint256 _tokenId, string calldata _uri) external onlyOwner {
   super._mint(_to, _tokenId);
   super._setTokenUri(_tokenId, _uri);
 }
}

然后你需要去编译它，以下图所示：

![](https://img-blog.csdnimg.cn/img_convert/34682e12f2fd7493a0460ba52d0a484b.png)

一旦智能合约编译完成，就可以部署它了！

使用 Inject Web3 部署智能合约并确保它已连接到您的 Metamask 的 Ropsten 测试网。

![](https://img-blog.csdnimg.cn/img_convert/0fda783db24984783e6010892ba5a91e.png)

单击部署后，它会提示您的 Metamask 确认合约部署。

![](https://img-blog.csdnimg.cn/img_convert/96169110005f5dde4157bbc6428b1a3b.png)

单击确认继续并部署合同。在这种情况下，我们在测试 Ether 中支付我们的 gas 费用，但如果你使用的是以太坊网络，您将不得不向矿工支付实际费用。

恭喜！您的智能合约现已部署！你甚至可以去以太坊浏览器检查你的新智能合约！

![](https://img-blog.csdnimg.cn/img_convert/71c87fa06aa56fe6353402d92d9eef06.png)

**铸造NFT**

现在转到 Deployed Contracts 部分并展开你的智能合约。

![](https://img-blog.csdnimg.cn/img_convert/84b4f8d2211cf4d42c90717e3a1bc2f2.png)

此外，扩展 mint 函数并添加以下详细信息：

1. 在 _to 字段中添加您的 Ropsten 地址
1. 在 _tokenid 字段中输入任何数字值（最好是几位数字）
1. 将您的 IPFS URL 添加到我们在 IPFS 部分获得的 _uri 字段

最后，点击交易并在 Metamask 上确认您的交易！

好极了！！！你的 NFT 是铸造的！您可以使用新的智能合约铸造任意数量的 NFT！

要检查您是否真的铸造了 NFT，您可以在 Remix 上查看它，或者通过单击 Metamask 来检查交易，或者打开以太坊浏览器（Etherscan）查看： [https://ropsten.etherscan.io/](https://ropsten.etherscan.io/tx/0xfd78181dfacc866804e50f731c482d33c002301f51d498dc32d50fce8419539b "https://ropsten.etherscan.io/")

恭喜！你已经从头开始创建了自己的 NFT 智能合约和 NFT ！您现在可以将其发送给朋友或者去以太坊主网实战，并以一百万美元的价格出售它！


