---
layout: post
title: 记github仓库被DMCA take down经历
date: 2019-06-25 20:19:00.000000000 +09:00
tags: DMCA github
---


# 记一次github仓库被DMCA take down的经历

**Note. 前段时间有点懒，大概有一个月没打理过博客，导致博客因为DMCA而被github官方临时关掉都没有及时发现。多亏了一位盆友提醒才知道自己博客被临时关停，不然按照这个懒惰的势头来看，没个一年半载都不会发现的 =。=**

**今天咱们来回顾下这次蛋疼的经历，介绍一下什么是DMCA，github仓库被DMCA take down了怎么办以及如何挽回被关掉的仓库。**

- [1. 博客变404](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-06-25-%E8%AE%B0github%E4%BB%93%E5%BA%93%E8%A2%ABDMCA%20take%20down%E7%BB%8F%E5%8E%86.md#1-%E5%8D%9A%E5%AE%A2%E5%8F%98404%E4%BA%86)

- [2. 原来是github仓库被DMCA take down（拔网线）了](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-06-25-%E8%AE%B0github%E4%BB%93%E5%BA%93%E8%A2%ABDMCA%20take%20down%E7%BB%8F%E5%8E%86.md#2-%E5%8E%9F%E6%9D%A5%E6%98%AFgithub%E4%BB%93%E5%BA%93%E8%A2%ABdmca-take-down%E6%8B%94%E7%BD%91%E7%BA%BF%E4%BA%86)

- [3. 还能抢救一下，真的就一下](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-06-25-%E8%AE%B0github%E4%BB%93%E5%BA%93%E8%A2%ABDMCA%20take%20down%E7%BB%8F%E5%8E%86.md#3-%E8%BF%98%E8%83%BD%E6%8A%A2%E6%95%91%E4%B8%80%E4%B8%8B%E7%9C%9F%E7%9A%84%E5%B0%B1%E4%B8%80%E4%B8%8B)

- [4. 这次蛋疼的经历总结](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-06-25-%E8%AE%B0github%E4%BB%93%E5%BA%93%E8%A2%ABDMCA%20take%20down%E7%BB%8F%E5%8E%86.md#4-%E8%BF%99%E6%AC%A1%E8%9B%8B%E7%96%BC%E7%9A%84%E7%BB%8F%E5%8E%86%E6%80%BB%E7%BB%93)

- [5. 参考资料](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-06-25-%E8%AE%B0github%E4%BB%93%E5%BA%93%E8%A2%ABDMCA%20take%20down%E7%BB%8F%E5%8E%86.md#5-%E5%8F%82%E8%80%83%E8%B5%84%E6%96%99)

## 1. 博客变404了

`"你是不是把我设置成你的博客黑名单了?"`

在一个风和日丽的下午，一位朋友@何原给我发来这消息（在这里表示感谢提醒）。毕竟这个博客也是没有几个点击量的，拉黑这种事情是不存在的。然后打开博客主页：http://berryjam.github.io ，竟然返回发现404，顿时菊花一紧。毕竟自己坚持写了两年的文章都在这上面，一瞬间都没了心里咯噔了一下。

当时第一反应是账号被盗，博客仓库被删了。然而当时打开仓库地址的时候，出现图1所示的情况。

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/2019-06-25/dmca-cut.png?raw=true" >
</div>

<p align="center">
  <b>图 1 仓库因为涉嫌侵权被DMCA take down示意图</b><br>
</p>

至此找到了博客404的原因了，但是DMCA是什么？为什么会被github官方DMCA take down？为了抢救博客，又开始了一番google。

## 2. 原来是github仓库被DMCA take down（拔网线）了

《数字千禧年版权法》（Digital Millennium Copyright ACT，简称DMCA）是一个美国著作权法律，旨在保护网上作品著作权。这里推荐下阮一峰老师多年前写的一篇博客[美国人怎么拔网线----DMCA入门](http://www.ruanyifeng.com/blog/2010/03/dmca.html)。简而言之，就是如果网络上有侵犯到作者的著作权的情况，作者本人就可以利用DMCA对侵犯行为处理。

在4月份的时候还发生过B站源码泄漏的事件，当时[B站就向github申请DMCA take down](https://www.oschina.net/news/106201/github-publishes-dmca-takedown-from-bilibili)关闭了源仓库和很多fork的仓库。所以有时候要学会利用DMCA保护合法著作权是一件好事，但是过度滥用就有点令人讨厌了。

回到我这边来，17年的时候在个人博客写过一篇如何在本地搭建IDEA的license server的教程，并提供了相关docker镜像。其实在那篇博客里面，已经声明了这只是为了提供了一种在本地试用激活后IDE的高能功能的方法，请不要用于生产环境，在文章最后也附上了正版购买链接。没想到jetbrain公司在[5月28日提交了违反DMCA的申请](https://github.com/github/dmca/blob/master/2019/05/2019-05-28-Jetbrains.md)，导致整个github仓库被临时关闭。而博客用的github page，所以博客就变404了。但是留意到github上只是注明了临时关闭，并说明了DMCA政策和如何发起DMCA takedown反诉申请，个人当时觉得就还有机会恢复，于是又开始了新一轮google......

## 3. 还能抢救一下，真的就一下

当时心里比较急，没细看github的DMCA政策导致恢复过程有点曲折。其实里面提到了在正式关闭前，会向仓库owner发起邮件提醒，在一天内删除涉嫌侵权内容。但是考虑到owner有可能因为放假或者不经常查收邮件等等各种原因，错过了修改窗口的owner（碰巧那周比较忙，没怎么看邮件-。-）还提供了最后一次补救机会。

关于补救方法在[DMCA take down政策](https://www.baidu.com/link?url=UBrUG9zW24G8kBmWu9vPjeJL9eFdNuhmTYh3QGza8skbozj5jP4-f9Iqm9IgSXIE_zqtwIOJk34ZChR2rpFr4K&wd=&eqid=f85b39680006d29e000000035d22ba42)里有详细介绍。大致分为2种：

- 硬刚，适合头铁人士。发起DMCA takedown counter notice，客观陈述自己是一个良好公民，并没有侵权。
- 老实听话删除相关涉嫌侵权内容，并**告知github developer team已经删除**（这很重要），要是删了没发邮件告知，仍然会被关闭，连最后一次机会都会被浪费掉。

我自己刚开始发起了DMCA takedown counter notice，描述了个人的某篇博客无意侵犯到相关著作权，申请解封仓库并承诺删除相关内容。然并卵，过了3、4周才收到github客服回复。后面留意到还有额外的一次补救窗口机会，果断又给客服发邮件申请。给developer@githubsupport.com邮箱，具体可[Inadvertently Missed the Window to Make Changes? ](https://help.github.com/en/articles/dmca-takedown-policy#c-what-if-i-inadvertently-missed-the-window-to-make-changes)。发完邮件大概过了4、5天，在周六的凌晨4点（大概是美国的下午4点左右）收到github客服的回复邮件，并明确在下周一开启窗口。一定要好好把握好最后一次机会。

[删除相关内容以及所有commite的副本](https://help.github.com/en/articles/removing-sensitive-data-from-a-repository)的方式有2种，记得需要先向github支持团队申请reopen仓库：

- 1.使用bfg工具进行删除

完全删除文件

```
$ bfg --delete-files YOUR-FILE-WITH-SENSITIVE-DATA
```

替换文件内容

```
$ bfg --replace-text passwords.txt
```

- 2.使用git filter-branch进行删除，具体方式请参考上面的链接。


还有最后一种是重建仓库，这是实在无法清理干净历史数据才会选择的方法。因为这种方式会丢失所有的历史提交记录、issuse、pr、star、fork等等。

## 4. 这次蛋疼的经历总结

回顾这次经历，个人有以下体会和建议：

1. 要有版权意识，无论是故意还是无意的，要尊重别人的知识产权
2. 对于著作权有异议的，可以发起DMCA take down反诉申请，维护自己合法权益
3. 不要把github仓库当作中心化仓库，不然被关闭或者误删了，内容不好找回来
4. 定期查收邮件
5. 及时删除侵权内容，并告知github developer team
6. 中美存在时差，就我个人来说，大概相差12个小时，不要太着急
7. 删除相关内容的同时，也需要把以往历史中的commit副本删掉

## 5. 参考资料

[1][阮一峰的网络日志：美国人怎么拔网线----DMCA入门](http://www.ruanyifeng.com/blog/2010/03/dmca.html)

[2][你的 Github 仓库被 DMCA Takedown 后怎么办？](https://linux.cn/article-9374-1.html)

