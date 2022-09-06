---
layout:     post
title:      【区块链 | ENS】以太坊（Ethereum）中的ENS架构中的注册表是什么，有什么接口实现域名注册功能？
subtitle:   【区块链 | ENS】以太坊（Ethereum）中的ENS架构中的注册表是什么，有什么接口实现域名注册功能？
date:       2022-06-15 08:41:31
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - ENS技术架构指南【系统分析】
    - 区块链
    - 以太坊
---

>【区块链 | ENS】以太坊（Ethereum）中的ENS架构中的注册表是什么，有什么接口实现域名注册功能？

# 【区块链 | ENS】以太坊（Ethereum）中的ENS架构中的注册表是什么，有什么接口实现域名注册功能？


**目录**

[ENS 注册表](#ENS%20%E6%B3%A8%E5%86%8C%E8%A1%A8)

[获取所有者](#%E8%8E%B7%E5%8F%96%E6%89%80%E6%9C%89%E8%80%85)

[获取解析器](#%E8%8E%B7%E5%8F%96%E8%A7%A3%E6%9E%90%E5%99%A8)

[获取 TTL](#%E8%8E%B7%E5%8F%96-TTL)

[设置所有者](#%E8%AE%BE%E7%BD%AE%E6%89%80%E6%9C%89%E8%80%85)

[设置解析器](#%E8%AE%BE%E7%BD%AE%E8%A7%A3%E6%9E%90%E5%99%A8)

[设置 TTL](#%E8%AE%BE%E7%BD%AE-TTL)

[设置子名称所有者](#%E8%AE%BE%E7%BD%AE%E5%AD%90%E5%90%8D%E7%A7%B0%E6%89%80%E6%9C%89%E8%80%85)

