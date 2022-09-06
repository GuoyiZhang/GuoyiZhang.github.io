---
layout:     post
title:      以太坊：MetaMask连接私有网络调用合约方法时报错： [ethjs-rpc] rpc error with payload Error: invalid sender
subtitle:   以太坊：MetaMask连接私有网络调用合约方法时报错： [ethjs-rpc] rpc error with payload Error: invalid sender
date:       2022-07-14 17:44:57
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 以太坊
    - rpc
    - 区块链
---

>以太坊：MetaMask连接私有网络调用合约方法时报错： [ethjs-rpc] rpc error with payload Error: invalid sender

# 以太坊：MetaMask连接私有网络调用合约方法时报错： [ethjs-rpc] rpc error with payload Error: invalid sender


错误详情： 

[ethjs-rpc] rpc error with payload
 {"id":7663982154336,"jsonrpc":"2.0","params": 
["0xf86b808504a817c800833d090094001a4039eed5a5099b2bd25085b48ef137902be38084be9a65558 
207f2a0aff9e56abb6bbeee508bf3fc3918176df97ae118b24bf78d90a9edb762900c1fa0649f391910b8 
2dc97f3259f0d781dde56bf1ed710d5723eeea8fc63bb351a48d"], 
"method":"eth_sendRawTransaction"}Error: invalid sender 
原因：

MetaMask配置私有网络时，chainId和以太坊节点的network不一致。

解决办法：

方法一：

1.查询以太坊私链的network, 我这里使用的是geth，查询方法如下：

进入geth控制台，输入指令web3.version,可以看到

2.chainid在创世区块中就已经设置了，所以你要去查看自己的chainid，然后设置到MetaMask。

3 将MateMask里面的chainID改为72（设置->网络->URL->保存） 

方法二：
每次交易前：

点击MateMask的设置里面的重设账户（Reset account）按钮。

————————————————
版权声明：本文为CSDN博主「小陈同学，，」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/ping_lvy/article/details/105333275

