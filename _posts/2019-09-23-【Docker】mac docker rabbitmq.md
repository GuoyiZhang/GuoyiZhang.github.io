---
layout:     post
title:      【Docker】mac docker rabbitmq
subtitle:   【Docker】mac docker rabbitmq
date:       2019-09-23 21:53:58
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Docker
---

>【Docker】mac docker rabbitmq

# 【Docker】mac docker rabbitmq

docker pull rabbitmq:management docker run -d --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:management docker logs rabbitmq

默认用户 guest

密码 guest

