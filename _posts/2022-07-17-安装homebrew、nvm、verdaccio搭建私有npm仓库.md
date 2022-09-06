---
layout:     post
title:      安装homebrew、nvm、verdaccio搭建私有npm仓库
subtitle:   安装homebrew、nvm、verdaccio搭建私有npm仓库
date:       2022-07-17 23:39:16
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 前端
    - npm
    - 前端
    - node.js
---

>安装homebrew、nvm、verdaccio搭建私有npm仓库

# 安装homebrew、nvm、verdaccio搭建私有npm仓库


# 1. Homebrew国内如何自动安装（国内地址）（Mac & Linux）

## 自动脚本(全部国内地址)（复制下面一句脚本到终端中粘贴回车)

**苹果电脑 常规安装脚本（推荐 完全体 几分钟安装完成）：**
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"

**苹果电脑 极速安装脚本（精简版 几秒钟安装完成）：**

/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)" speed

-> [Mac电脑如何打开终端：command+空格 在聚焦搜索中输入terminal回车](https://link.zhihu.com/?target=https%3A//support.apple.com/zh-cn/guide/terminal/apd5265185d-f365-44cb-8b09-71a064a42125/mac "Mac电脑如何打开终端：command+空格 在聚焦搜索中输入terminal回车")*。*

**苹果电脑 卸载脚本：**
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/HomebrewUninstall.sh)"

**常见错误**去下方[地址](https://link.zhihu.com/?target=https%3A//gitee.com/cunkai/HomebrewCN/blob/master/error.md "地址")查看

https://gitee.com/cunkai/HomebrewCN/blob/master/error.md

**Linux电脑** 安装脚本：

rm Homebrew.sh ; wget https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh ; bash Homebrew.sh

**Linux电脑** 卸载脚本：

rm HomebrewUninstall.sh ; wget https://gitee.com/cunkai/HomebrewCN/raw/master/HomebrewUninstall.sh ; bash HomebrewUninstall.sh

# 2. nvm安装

代码库： 
https://github.com/nvm-sh/nvm/#install--update-script

下载代码库

安装：sh install.sh profile file (~/.bash_profile, ~/.zshrc, ~/.profile, or ~/.bashrc). export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")" [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" /# This loads nvm 刷新全局配置 bash: source ~/.bashrc zsh: source ~/.zshrc ksh: . ~/.profile 安装node nvm instll 14

# 3. verdaccio搭建私有npm仓库

安装 npm install -g verdaccio 运行 verdaccio 打开 http://localhost:4873/ 1. Login npm adduser --registry http://localhost:4873/ 2. Publish npm publish --registry http://localhost:4873/ 3. Refresh this page

参考1：[Homebrew国内如何自动安装（国内地址）（Mac & Linux） - 知乎](https://zhuanlan.zhihu.com/p/111014448 "Homebrew国内如何自动安装（国内地址）（Mac & Linux） - 知乎")

参考2：[https://github.com/nvm-sh/nvm](https://github.com/nvm-sh/nvm "https://github.com/nvm-sh/nvm")

参考3：[使用Verdaccio搭建私有npm仓库 - 简书](https://www.jianshu.com/p/a13dd18782cf "使用Verdaccio搭建私有npm仓库 - 简书")

