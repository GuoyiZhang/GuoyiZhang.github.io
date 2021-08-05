---
layout:     post
title:      【区块链 | EOS】如何正常停止nodeos？（database dirty flag set (likely due to unclean shutdown): replay required）
subtitle:   【区块链 | EOS】如何正常停止nodeos？（database dirty flag set (likely due to unclean shutdown): replay required）
date:       2018-12-27 18:01:08
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - EOS
---

>【区块链 | EOS】如何正常停止nodeos？（database dirty flag set (likely due to unclean shutdown): replay required）

# 【区块链 | EOS】如何正常停止nodeos？（database dirty flag set (likely due to unclean shutdown): replay required）

```
kill 命令再次执行时会导致以下错误。
pkill -9 nodeos or kill -9 {pid} database dirty flag set (likely due to unclean shutdown): replay required
```
当重新运行nodeos时，必须使用--replay-blockchain命令忽略它。

让我们安全的结束nodeos 进程
This appears to be two different issues. Startup after a crash or ungraceful shutdown nearly always fails due to corruption of the boost shared memory cache of the in-memory database. --resync is required to clean up the mess. For normal shutdown, never kill with -9. Always use either the default (no argument) signal (which is SIGTERM) or SIGINT. Numerically, those are 15 and 2, respectively. pkill nodeos | Safe pkill -15 nodeos | Safe pkill -2 nodeos | Safe pkill -TERM nodeos | Safe pkill -SIGTERM nodeos | Safe pkill -INT nodeos | Safe pkill -SIGINT nodeos | Safe pkill -9 nodeos | Not Safe pkill -KILL nodeos | Not Safe pkill -SIGKILL nodeos | Not Safe The core dump is a different problem. That looks like a corrupted network packet, specifically a signed_block_summary. Summary messages are being eliminated from the protocol, so this particular error will no longer be possible soon.

##  

##  

## **下次再次启动使用命令为**

    nodeos -e -p eosio \ --plugin eosio::producer_plugin \ --plugin eosio::chain_api_plugin \ --plugin eosio::http_plugin \ --plugin eosio::history_plugin \ --plugin eosio::history_api_plugin \ --data-dir /home/contracts/eosio/data \ --config-dir /home/contracts/eosio/config \ --access-control-allow-origin='/*' \ --contracts-console \ --http-validate-host=false \ --verbose-http-errors \ --filter-on='/*' >> nodeos.log 2>&1 &

 

# 总结

总结： 停止进程的命令： pkill nodeos 开启命令为： nodeos -e -p eosio \ --plugin eosio::producer_plugin \ --plugin eosio::chain_api_plugin \ --plugin eosio::http_plugin \ --plugin eosio::history_plugin \ --plugin eosio::history_api_plugin \ --data-dir /home/contracts/eosio/data \ --config-dir /home/contracts/eosio/config \ --access-control-allow-origin='/*' \ --contracts-console \ --http-validate-host=false \ --verbose-http-errors \ --filter-on='/*' >> nodeos.log 2>&1 &

参考自：[github](https://github.com/EOSIO/eos/issues/4301)

 

 

