---
layout:     post
title:      web3j开发以太坊智能合约快速入门(特别适合java和android开发者)
subtitle:   web3j开发以太坊智能合约快速入门(特别适合java和android开发者)
date:       2019-04-22 15:26:51
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 区块链
    - ETH
---

>web3j开发以太坊智能合约快速入门(特别适合java和android开发者)

# web3j开发以太坊智能合约快速入门(特别适合java和android开发者)


# web3j简介

web3j是一个轻量级、高度模块化、响应式、类型安全的Java和Android类库提供丰富API，用于处理以太坊智能合约及与以太坊网络上的客户端(节点)进行集成。

可以通过它进行以太坊区块链的开发，而无需为你的应用平台编写集成代码。

# 可以快速启动dmeo示例

想要快速启动的话，有一个[Web3j demo示例项目](https://github.com/web3j/sample-project-gradle)可用，演示了通过Web3j开发以太坊的许多核心特征，其中包括：

* 连接到以太网网络上的节点
* 加载一个以太坊钱包文件
* 将以太币从一个地址发送到另一个地址
* 向网络部署智能合约
* 从部署的智能合约中读取值
* 更新部署的智能合约中的值
* 查看由智能合约记录的事件

# web3j开发入门

首先将最新版本的web3j安装到项目中。

## Maven

**Java 8:**
<dependency> <groupId>org.web3j</groupId> <artifactId>core</artifactId> <version>3.4.0</version> </dependency>

**Android:**

<dependency> <groupId>org.web3j</groupId> <artifactId>core</artifactId> <version>3.3.1-android</version> </dependency>

## Gradle

**Java 8:**
compile ('org.web3j:core:3.4.0')

**Android:**

compile ('org.web3j:core:3.3.1-android')

## 启动客户端

需要启动一个以太坊客户端，当然如果你已经启动了就不需要再次启动。

如果是[geth](https://github.com/ethereum/go-ethereum/wiki/geth)的话这么启动：
$ geth --rpcapi personal,db,eth,net,web3 --rpc --rinkeby

如果是[Parity](https://github.com/paritytech/parity)启动：

$ parity --chain testnet

如果使用[Infura](https://infura.io/)客户端提供的免费的云端服务，这么启动：

Web3j web3 = Web3j.build(new HttpService("https://morden.infura.io/your-token"));

如果想进一步的了解infura，请参阅[Using Infura with web3j](https://docs.web3j.io/infura.html)。

在网络上如何获得以太币的相关文档可以看这个：[testnet section of the docs](https://docs.web3j.io/transactions.html#ethereum-testnets)。

当不需要Web3j实例时，需要调用

shutdown
方法来释放它所使用的资源。
web3.shutdown()

## 发送请求

**发送同步请求**
Web3j web3 = Web3j.build(new HttpService()); // defaults to http://localhost:8545/ Web3ClientVersion web3ClientVersion = web3.web3ClientVersion().send(); String clientVersion = web3ClientVersion.getWeb3ClientVersion();

**使用CompletableFuture (Future on Android) 发送异步请求**

Web3j web3 = Web3j.build(new HttpService()); // defaults to http://localhost:8545/ Web3ClientVersion web3ClientVersion = web3.web3ClientVersion().sendAsync().get(); String clientVersion = web3ClientVersion.getWeb3ClientVersion();

/***使用RxJava的Observable**

Web3j web3 = Web3j.build(new HttpService()); // defaults to http://localhost:8545/ web3.web3ClientVersion().observable().subscribe(x -> { String clientVersion = x.getWeb3ClientVersion(); ... });

**注意Android使用方式**

Web3j web3 = Web3jFactory.build(new HttpService()); // defaults to http://localhost:8545/ ...

## IPC

Web3j还支持通过文件套接字快速运行进程间通信（IPC），支持客户端在相同的主机上同时运行Web3j。在创建服务时，使用相关的

IPCService
就可以实现而不需要通过

HTTPService
。
// OS X/Linux/Unix: Web3j web3 = Web3j.build(new UnixIpcService("/path/to/socketfile")); ... // Windows Web3j web3 = Web3j.build(new WindowsIpcService("/path/to/namedpipefile")); ...

**需要注意：IPC通信在web3j-android中不可用。**

## 通过java打包以太坊智能合约

Web3j可以自动打包智能合同代码，以便在不脱离JVM的情况下进行以太坊智能合同部署和交互。

要打包代码，需要先编译智能合同：
$ solc <contract>.sol --bin --abi --optimize -o <output-dir>/

然后用[web3j的命令行工具](https://docs.web3j.io/command_line.html)打包代码：

web3j solidity generate /path/to/<smart-contract>.bin /path/to/<smart-contract>.abi -o /path/to/src/main/java -p com.your.organisation.name

接下来就可以新建和部署智能合约了：

Web3j web3 = Web3j.build(new HttpService()); // defaults to http://localhost:8545/ Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile"); YourSmartContract contract = YourSmartContract.deploy( <web3j>, <credentials>, GAS_PRICE, GAS_LIMIT, <param1>, ..., <paramN>).send(); // constructor params

或者使用一个现有的智能合约：

YourSmartContract contract = YourSmartContract.load( "0x<address>|<ensName>", <web3j>, <credentials>, GAS_PRICE, GAS_LIMIT);

然后就可以进行智能合约的交互了：

TransactionReceipt transactionReceipt = contract.someMethod( <param1>, ...).send();

调用智能合约：

Type result = contract.someMethod(<param1>, ...).send();

更多关于打包的资料可以看这里：[Solidity smart contract wrappers](https://docs.web3j.io/smart_contracts.html#smart-contract-wrappers)

## Filters

web3j的响应式函数可以使观察者通过事件去通知消息订阅者变得很简单，并能够记录在区块链中。接收所有新的区块并把它们添加到区块链中：
Subscription subscription = web3j.blockObservable(false).subscribe(block -> { ... });

接收所有新的交易并把它们添加到区块链中：

Subscription subscription = web3j.transactionObservable().subscribe(tx -> { ... });

接收所有已经提交到网络中等待处理的交易。(他们被统一的分配到一个区块之前。)

Subscription subscription = web3j.pendingTransactionObservable().subscribe(tx -> { ... });

或者你重置所有的区块到最新的位置，那么当有新建区块的时候会通知你。

Subscription subscription = catchUpToLatestAndSubscribeToNewBlocksObservable( <startBlockNumber>, <fullTxObjects>) .subscribe(block -> { ... });

**主题过滤**也被支持：

EthFilter filter = new EthFilter(DefaultBlockParameterName.EARLIEST, DefaultBlockParameterName.LATEST, <contract-address>) .addSingleTopic(...)|.addOptionalTopics(..., ...)|...; web3j.ethLogObservable(filter).subscribe(log -> { ... });

当不再需要时，订阅也应该被取消：

subscription.unsubscribe();

**注意：Infura中不支持filters。**

需要了解更多有关过滤器和事件的信息可以查看[Filters and Events](https://docs.web3j.io/filters.html)和[Web3jRx](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/protocol/rx/Web3jRx.java)的接口。

## 交易

Web3j支持使用以太坊钱包文件（推荐的）和用于发送事务的以太坊客户端管理命令。

使用以太钱包文件发送以太币给其他人：
Web3j web3 = Web3j.build(new HttpService()); // defaults to http://localhost:8545/ Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile"); TransactionReceipt transactionReceipt = Transfer.sendFunds( web3, credentials, "0x<address>|<ensName>", BigDecimal.valueOf(1.0), Convert.Unit.ETHER) .send();

或者你希望建立你自己定制的交易：

Web3j web3 = Web3j.build(new HttpService()); // defaults to http://localhost:8545/ Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile"); // get the next available nonce EthGetTransactionCount ethGetTransactionCount = web3j.ethGetTransactionCount( address, DefaultBlockParameterName.LATEST).send(); BigInteger nonce = ethGetTransactionCount.getTransactionCount(); // create our transaction RawTransaction rawTransaction = RawTransaction.createEtherTransaction( nonce, <gas price>, <gas limit>, <toAddress>, <value>); // sign & send our transaction byte[] signedMessage = TransactionEncoder.signMessage(rawTransaction, credentials); String hexValue = Numeric.toHexString(signedMessage); EthSendTransaction ethSendTransaction = web3j.ethSendRawTransaction(hexValue).send(); // ...

使用Web3j的[Transfer](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/tx/Transfer.java)进行以太币交易要简单得多。

使用以太坊客户端的管理命令（如果你的钱包密钥已经在客户端存储）：
Admin web3j = Admin.build(new HttpService()); // defaults to http://localhost:8545/ PersonalUnlockAccount personalUnlockAccount = web3j.personalUnlockAccount("0x000...", "a password").sendAsync().get(); if (personalUnlockAccount.accountUnlocked()) { // send a transaction }

如果你想使用 [Parity’s Personal](https://github.com/paritytech/parity/wiki/JSONRPC-personal-module) 或者 [Trace](https://github.com/paritytech/parity/wiki/JSONRPC-trace-module) 功能, 或者 Geth’s [Personal](https://github.com/ethereum/go-ethereum/wiki/Management-APIs#personal) 客户端 APIs，可以使用

org.web3j:parity
和

org.web3j:geth
模块。

## 命令行工具

web3j的jar包为每一个版本都提供命令行工具。命令行工具允许你直接通过一些命令使用web3j的一些功能：

* 钱包创建
* 钱包密码管理
* 资金从钱包转移到另一个
* solidity编写的智能合同功能打包

请参阅[文档](https://docs.web3j.io/command_line.html)以获得命令行相关的进一步的信息。

## 其他的细节

**java8 bulid：**

* Web3j提供对所有响应类型的安全访问。可选的或null响应java 8都支持。
* 异步请求包在一个java 8的[CompletableFutures](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html)。Web3j提供了围绕所有异步请求的打包工具，以确保在执行期间可以捕获任何异常，而不只是丢弃。由于在完全检查中会有很多缺少支持的异常情况，这些异常通常被确定为未检测到的异常，导致检测过程出现问题。有关详细信息，请参见 [Async.run()](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/utils/Async.java)及其关联 [test](https://github.com/web3j/web3j/blob/master/core/src/test/java/org/web3j/utils/AsyncTest.java)。

**在java 8的Android版本：**

* 包数量作为 [BigIntegers](https://docs.oracle.com/javase/8/docs/api/java/math/BigInteger.html)返回。对于简单的结果，可以通过

Response.getResult()
获取字符串类型的数量结果。
* 还可以通过在 HttpService和IpcService类中存在的

includeRawResponse
参数将原生的JSON包放置在响应中。

 
本文的web3j官方原文请看这里：[web3j](https://docs.web3j.io/getting_started.html)。

 


