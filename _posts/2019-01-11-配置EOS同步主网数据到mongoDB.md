---
layout:     post
title:      配置EOS同步主网数据到mongoDB
subtitle:   配置EOS同步主网数据到mongoDB
date:       2019-01-11 12:18:23
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - EOS
    - 区块链
    - MongoDb
---

>配置EOS同步主网数据到mongoDB

# 配置EOS同步主网数据到mongoDB


### EOS编译安装，请参考 [编译EOS主网EOS-Mainnet代码](https://www.bcskill.com/index.php/archives/405.html)

### 一. 修改Config配置

先运行下

nodeos
，将会自动创建

~/.local/share/eosio/nodeos/config
目录和

config.ini
文件。
修改

config.ini
中如下内容
//添加 （2018-8-18 此时可用，如果有想分享的节点，请私聊我） p2p-peer-address = fullnode.eoslaomao.com:443 p2p-peer-address = mars.fnp2p.eosbixin.com:443 //修改 可忽略 agent-name = "BcSkill" //如果需要返回错误信息,修改 verbose-http-errors = true //添加插件支持 plugin = eosio::chain_plugin plugin = eosio::net_plugin //修改mongodb插件相关配置 plugin = eosio::mongo_db_plugin mongodb-uri = mongodb://127.0.0.1:27017/EOS mongodb-filter-on = /* /#mongodb-filter-out = spammer:: mongodb-filter-out = eosio:onblock: mongodb-filter-out = gu2tembqgage:: mongodb-filter-out = blocktwitter:: mongodb-queue-size = 2048 abi-serializer-max-time-ms = 5000 mongodb-block-start = 1 mongodb-store-block-states = false mongodb-store-blocks = false mongodb-store-transactions = false mongodb-store-transaction-traces = true mongodb-store-action-traces = true read-mode = read-only

参考：[MongoDB Filtering and Optimizations](https://github.com/EOSIO/eos/pull/5043)
参考：[issues/5797](https://github.com/EOSIO/eos/issues/5797)

### 二. 安装配置并启动MongoDB

1. 安装MongoDB

先安装MongoDB 参考（[Ubuntu 安装 Mongodb 3+](https://www.bcskill.com/index.php/archives/327.html)）

2. 配置MongoDB

进入到MongoDB bin 目录，可以添加到环境变量，方便操作。
cd ~/opt/mongodb/bin

由于链上数据较大，比如1000W块左右的数据，离线压缩包大约14GB，同步到MongoDB大概需要200GB左右。所以需要将MongoDB数据单独磁盘存储。
修改MongoDB数据存储位置,

/mnt/data
为我这边挂载的存储盘
新建目录

mkdir /mnt/data/mongo/db

3. 启动MongoDB
mongod --dbpath /mnt/data/mongo/db

这时MongoDB服务会默认监听27017端口
![](https://www.bcskill.com/usr/uploads/2018/08/2398520095.png)

### 三. 下载主网离线数据包

参考 [EOS 主网数据更新，离线数据包](https://www.bcskill.com/index.php/archives/354.html)
因为使用离线包可以加快追上主网的进度，如果从零自己同步到主网进度，难以想象要多久...
假设已经将离线数据下载并解压到了 

/mnt/data/data
目录下。

### 四. 启动nodeos开始同步数据

nodeos --data-dir /mnt/data/data --hard-replay-blockchain

如果需要清空mongo数据库的话，添加

--mongodb-wipe
相关代码如下

eos\plugins\mongo_db_plugin\mongo_db_plugin.cpp

void mongo_db_plugin::plugin_initialize(const variables_map& options) { try { if( options.count( "mongodb-uri" )) { ilog( "initializing mongo_db_plugin" ); my->configured = true; if( options.at( "replay-blockchain" ).as<bool>() || options.at( "hard-replay-blockchain" ).as<bool>() || options.at( "delete-all-blocks" ).as<bool>() ) { if( options.at( "mongodb-wipe" ).as<bool>()) { ilog( "Wiping mongo database on startup" ); my->wipe_database_on_startup = true; } else if( options.count( "mongodb-block-start" ) == 0 ) { EOS_ASSERT( false, chain::plugin_config_exception, "--mongodb-wipe required with --replay-blockchain, --hard-replay-blockchain, or --delete-all-blocks" " --mongodb-wipe will remove all EOS collections from mongodb." ); } }

此时已开始，同步中
![](https://www.bcskill.com/usr/uploads/2018/08/77924755.png)

五. 服务器建议配置

内存：32GB
存储：1T+

