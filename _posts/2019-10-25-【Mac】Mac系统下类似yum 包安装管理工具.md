---
layout:     post
title:      【Mac】Mac系统下类似yum 包安装管理工具
subtitle:   【Mac】Mac系统下类似yum 包安装管理工具
date:       2019-10-25 14:58:22
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 开发工具
---

>【Mac】Mac系统下类似yum 包安装管理工具

# 【Mac】Mac系统下类似yum 包安装管理工具


第一次用Mac, 今天使用终端下开发包，以前用虚拟机时用的apt-get , yum，rpm竟然全都用不了。同样是linux系统，本白一脸懵逼

查了下资料，发现Mac系统是使用brew命令install

![](https://img-blog.csdn.net/20180605143425644?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0OTE1MzM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

还是不行，继续往源头上找资料，原来Mac自带ruby命令，需要先通过ruby安装brew。

安装brew
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

安装成功！使用brew install wegt 测试成功，问题解决！

![](https://img-blog.csdn.net/2018060514421875?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0OTE1MzM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

----

----

----

----

----

----

----

问题来了： Homebrew官网下载很慢！

怎么办？？？？

# mac解决homebrew官网安装慢的问题

1. 下载安装脚本
curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install >> brew_install

2. 官方安装慢，主要是因为需要下载github.com上的资源。替换安装文件中的源

替换下载的brew_install脚本中的
BREW_REPO = "https://github.com/Homebrew/brew".freeze
为
BREW_REPO = "https://mirrors.ustc.edu.cn/brew.git".freeze
 
tips：有些提示要替换CORE_TAP_REPO = “https://github.com/Homebrew/homebrew-core“.freeze，但是我根据1中的命令下载的脚本中，没有找到CORE_TAP_REPO选项，这里说明下系统版本:mojave 10.14.3。

3. 配置脚本之后，重新执行如命令

ruby brew_install

等待最后执行安装完成。

如果出现如下问题
Error: Failure while executing: git clone https://github.com/Homebrew/homebrew-core /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core --depth=1
Error: Failure while executing: /usr/local/bin/brew tap homebrew/core
是因为源的问题，需要更换git的源。请参照后续相关文章

