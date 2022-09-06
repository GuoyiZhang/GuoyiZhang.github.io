---
layout:     post
title:      【Mac | Git】Mac电脑github：Permission denied (publickey). fatal: Could not read from remote repository.
subtitle:   【Mac | Git】Mac电脑github：Permission denied (publickey). fatal: Could not read from remote repository.
date:       2019-10-24 10:27:35
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
---

>【Mac | Git】Mac电脑github：Permission denied (publickey). fatal: Could not read from remote repository.

# 【Mac | Git】Mac电脑github：Permission denied (publickey). fatal: Could not read from remote repository.


## [Permission denied (publickey). fatal: Could not read from remote repository.](https://www.cnblogs.com/wmr95/p/7852832.html)

博主在github上下载tiny face的的源代码的时候，遇到git clone命令为：git clone --recursive git@github.com:peiyunh/tiny.git

而当我在ternimal下执行这条语句的时候，出现错误：
Permissiondenied (publickey).

fatal:Could not read from remote repository.

Pleasemake sure you have the correct access rights

and the repository exists.

但是其实执行命令：git clone git@github.com:peiyunh/tiny.git 是没有问题的（不加--recursive参数），于是百度了一番，我理解的是原因是由于你在本地（或者服务器上）没有生成ssh key，你可以在ternimal下执行：

cd ~/.ssh ls来查看是否有文件id_rsa以及文件id_rsa.pub，如下图所示：（我的已经生成了，所以我ls后会显示。）

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMyMDE3LmNuYmxvZ3MuY29tL2Jsb2cvMTA3OTY4OS8yMDE3MTEvMTA3OTY4OS0yMDE3MTExNzE3NTA1ODcwMi0xOTQ4ODE0MDA2LnBuZw?x-oss-process=image/format,png)

下面记录下解决办法：

1.首先，如果你没有ssh key的话，在ternimal下输入命令：ssh-keygen -t rsa -C "youremail@example.com"， youremail@example.com改为自己的邮箱即可，途中会让你输入密码啥的，不需要管，一路回车即可，会生成你的ssh key。（如果重新生成的话会覆盖之前的ssh key。）

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMyMDE3LmNuYmxvZ3MuY29tL2Jsb2cvMTA3OTY4OS8yMDE3MTEvMTA3OTY4OS0yMDE3MTExNzE3NTQzMjMyNy0xNDEwNTQzNDIyLnBuZw?x-oss-process=image/format,png)

2.然后再ternimal下执行命令：

ssh -v git@github.com

最后两句会出现：

No more authentication methods to try.  

Permission denied (publickey).

3.这时候再在ternimal下输入：

ssh-agent -s

然后会提示类似的信息：

SSH_AUTH_SOCK=/tmp/ssh-GTpABX1a05qH/agent.404; export SSH_AUTH_SOCK;  

SSH_AGENT_PID=13144; export SSH_AGENT_PID;  

echo Agent pid 13144;

4.接着再输入：　　
ssh-add ~/.ssh/id_rsa

这时候应该会提示：

Identity added: ...（这里是一些ssh key文件路径的信息）

（注意）如果出现错误提示：

Could not open a connection to your authentication agent.

请执行命令：eval `ssh-agent -s`后继续执行命令 ssh-add ~/.ssh/id_rsa，这时候一般没问题啦。

5.打开你刚刚生成的id_rsa.pub，将里面的内容复制，进入你的github账号，在settings下，SSH and GPG keys下new SSH key，title随便取一个名字，然后将id_rsa.pub里的内容复制到Key中，完成后Add SSH Key。

6.最后一步，验证Key

在ternimal下输入命令：
ssh -T git@github.com

提示：Hi xxx! You've successfully authenticated, but GitHub does not provide shell  access.

这时候你的问题就解决啦，可以使用命令 git clone --recursive git@github.com:peiyunh/tiny.git 去下载你的代码啦。

 

