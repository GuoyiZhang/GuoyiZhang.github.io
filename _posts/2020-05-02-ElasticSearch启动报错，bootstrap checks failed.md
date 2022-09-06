---
layout:     post
title:      ElasticSearch启动报错，bootstrap checks failed
subtitle:   ElasticSearch启动报错，bootstrap checks failed
date:       2020-05-02 19:31:22
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Java
---

>ElasticSearch启动报错，bootstrap checks failed

# ElasticSearch启动报错，bootstrap checks failed


修改elasticsearch.yml配置文件，允许外网访问。

vim config/elasticsearch.yml
/# 增加

network.host: 0.0.0.0

启动失败，检查没有通过，报错

[2018-05-18T17:44:59,658][INFO ][o.e.b.BootstrapChecks    ] [gFOuNlS] bound or publishing to a non-loopback address, enforcing bootstrap checks
ERROR: [2] bootstrap checks failed
[1]: max file descriptors [4096] for elasticsearch process is too low, increase to at least [65536]

[2]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

[1]: max file descriptors [65535] for elasticsearch process is too low, increase to at least [65536]

编辑 /etc/security/limits.conf，追加以下内容；
/* soft nofile 65536
/* hard nofile 65536
此文件修改后需要重新登录用户，才会生效

[2]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

编辑 /etc/sysctl.conf，追加以下内容：
vm.max_map_count=655360
保存后，执行：
sysctl -p

重新启动，成功。

bin/elasticsearch &
 

