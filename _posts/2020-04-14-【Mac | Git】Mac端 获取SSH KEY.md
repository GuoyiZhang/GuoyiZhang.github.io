# 【Mac | Git】Mac端 获取SSH KEY


## **一、判断是否有已有SSH文件**

ls -al ~/.ssh

如果显示如下图所示，即为没有ssh文件

No such file or directory

## **二、生成新的SSH KEY**

在终端输入命令：
ssh-keygen -t rsa -C "github注册过的邮箱"

默认会在相应路径下（/your_home_path）生成id_rsa和id_rsa.pub两个文件，如下面代码所示

Generating public/private rsa key pair. Enter file in which to save the key (/Users/zhujianyi/.ssh/id_rsa):

## **三、输入passphrase（可以不输入）**

Enter passphrase (empty for no passphrase): Enter same passphrase again:

结果：

Your identification has been saved in /Users/zhujianyi/.ssh/id_rsa. Your public key has been saved in /Users/zhujianyi/.ssh/id_rsa.pub. The key fingerprint is: SHA256:jdasjdkajs;dja;jkdja;jskda; your_email@example.com

## **四、拷贝SSH KEY信息**

pbcopy < ~/.ssh/id_rsa.pub

 


