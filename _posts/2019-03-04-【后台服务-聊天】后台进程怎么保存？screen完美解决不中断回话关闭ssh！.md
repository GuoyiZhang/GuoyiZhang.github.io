# 【后台服务-聊天】后台进程怎么保存？screen完美解决不中断回话关闭ssh！


## 1. 发现问题

近期在使用workman做聊天后端时候，发现Linux服务器只要关闭了ssh窗口，会话就失效了，会话自动被停止，怎么解决这个问题呢？

## 2. 解决问题

1. & 解决问题尝试中。。。。

经过一番测试，发现&并不能解决问题！

2. screen

经了解，发现screen可以开启新的会话窗口，并达到关闭ssh不清理进程效果，于是把imchat后台程序放进了screen新窗口中。

## 3. 分析总结

进入screen screen 显示screen进程列表 screen -ls 进入某screen screen -r <id> 切换到ssh 按键：Ctrl+A+D

 


