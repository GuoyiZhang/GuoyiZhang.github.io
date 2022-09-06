---
layout:     post
title:      (error) MISCONF Redis is configured to save RDB snapshots, but is currently not able to persist on d
subtitle:   (error) MISCONF Redis is configured to save RDB snapshots, but is currently not able to persist on d
date:       2018-12-11 13:53:55
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Redis
---

>(error) MISCONF Redis is configured to save RDB snapshots, but is currently not able to persist on d

# (error) MISCONF Redis is configured to save RDB snapshots, but is currently not able to persist on d


[(error) MISCONF Redis is configured to save RDB snapshots, but is currently not able to persist on disk.](https://www.cnblogs.com/anny-1980/p/4582674.html)

今天运行Redis时发生错误，错误信息如下：

(error) MISCONF Redis is configured to save RDB snapshots, but is currently not able to persist on disk. Commands that may modify the data set are disabled. Please check Redis logs for details about the error.

Redis被配置为保存数据库快照，但它目前不能持久化到硬盘。用来修改集合数据的命令不能用。请查看Redis日志的详细错误信息。

 

原因：

强制关闭Redis快照导致不能持久化。

 

解决方案：

运行config set stop-writes-on-bgsave-error no　命令后，关闭配置项stop-writes-on-bgsave-error解决该问题。

root@ubuntu:/usr/local/redis/bin/# ./redis-cli
127.0.0.1:6379> config set stop-writes-on-bgsave-error no
OK
127.0.0.1:6379> lpush myColour "red"
(integer) 1

