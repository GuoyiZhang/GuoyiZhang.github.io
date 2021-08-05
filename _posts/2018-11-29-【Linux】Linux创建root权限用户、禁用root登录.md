---
layout:     post
title:      【Linux】Linux创建root权限用户、禁用root登录
subtitle:   【Linux】Linux创建root权限用户、禁用root登录
date:       2018-11-29 10:06:06
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Linux
---

>【Linux】Linux创建root权限用户、禁用root登录

# 【Linux】Linux创建root权限用户、禁用root登录


# 一、创建root权限用户步骤

## 1、创建用户yanmin

A、adduser yanmin
//创建用户 [root@iZrj98p4hhys0y9fdxmcy4Z ~]/# adduser yanmin

B、设置用户密码

//输入两次密码 [root@iZrj98p4hhys0y9fdxmcy4Z ~]/# passwd yanmin Changing password for user yanmin. New password: Retype new password: passwd: all authentication tokens updated successfully

## 2、赋予yanmin用户root权限

A、第一种方案：修改/etc/sudoers文件，找到下面一行，在root下面添加一行

修改/etc/sudoers文件需要
sudo vim /etc/sudoers
 
/# User privilege specification root    ALL=(ALL:ALL) ALL yanmin  ALL=(ALL:ALL) ALL

B、第二种方案:修改/etc/passwd文件，找到如下行，把用户ID修改为0,如下：

yanmin:x:0:1000:yanmin,,,:/home/yanmin:/bin/bash

## 3、测试用户yanmin登录

ssh yanmin@xx.xx.xx.xx

## 4、如果想免密码登陆，请参考

[Linux使用公钥ssh登录](http://yanmin99.com/2017/07/12/Linux%E4%BD%BF%E7%94%A8%E5%85%AC%E9%92%A5ssh%E7%99%BB%E5%BD%95/)

# 二、禁用root登录步骤

1、准备工作

注意：禁用root登陆之前，一定要确认其他用户可以登录，并且具备root权限
2、把/etc/ssh/sshd_config中(PermitRootLogin no)设置YES
PermitRootLogin yes修改为 PermitRootLogin no

3、重启SSH daemon服务

//centos /etc/init.d/sshd restart //ubuntu //如果ssh重启没有效果,就重启系统 /etc/init.d/ssh restart shutdown -r now

4、尝试远程登陆

yanmin:~ yanmin$ ssh root@gitlab root@gitlab's password: Permission denied, please try again.

 


