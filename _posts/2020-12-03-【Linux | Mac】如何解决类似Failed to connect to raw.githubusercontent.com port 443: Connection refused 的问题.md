# 【Linux | Mac】如何解决类似Failed to connect to raw.githubusercontent.com port 443: Connection refused 的问题


### 背景

笔者最近发现 github 的用户头像和自己文章中的图片显示不出来了。然后今天发现安装 homeBrew 和 nvm 出现了标题的报错信息。

[![image](https://img-blog.csdnimg.cn/img_convert/0c3a058e3e08dfbb1878918fd6cc30e1.png)](https://user-images.githubusercontent.com/11072796/82433212-b5f11f80-9ac3-11ea-886e-6fe17edc1d2e.png)

以上是安装 pnpm 的报错信息，可以发现，脚本需要到 raw.githubusercontent.com 上拉取代码。

网上搜索了一下，发现是 github 的一些域名的 DNS 解析被污染，导致DNS 解析过程无法通过域名取得正确的IP地址。

### DNS 污染

[DNS 污染](https://zhuanlan.zhihu.com/p/101908711)，感兴趣的朋友可以去了解一下。

### 解决方案

打开 [https://www.ipaddress.com/](https://www.ipaddress.com/) 输入访问不了的域名

[![image](https://img-blog.csdnimg.cn/img_convert/efc23d8912bc3c5f5bf121c54a2fca4b.png)](https://user-images.githubusercontent.com/11072796/82434255-2e0c1500-9ac5-11ea-8102-9ebe8475ea34.png)

查询之后可以获得正确的 IP 地址

在本机的 host 文件中添加，建议使用 [switchhosts](https://github.com/oldj/SwitchHosts/releases) 方便 host 管理

199.232.68.133 raw.githubusercontent.com
199.232.68.133 user-images.githubusercontent.com
199.232.68.133 avatars2.githubusercontent.com
199.232.68.133 avatars1.githubusercontent.com

添加以上几条 host 配置，页面的图片展示就正常了，homebrew 也能装了，nvm 也行动灵活了。

 

使用SwitchHosts配置host之后
[![截屏2020-07-08 下午12 58 02](https://img-blog.csdnimg.cn/img_convert/4a97c0f9c0b548996b4317747fbc4e49.png)](https://user-images.githubusercontent.com/10241716/86878061-c19fb080-c11a-11ea-918a-424f4dc5ccd2.png)

一直失败的RVM安装可以正常安装了
[![截屏2020-07-08 下午12 58 24](https://img-blog.csdnimg.cn/img_convert/7430198697d322d9ff49529438ab3291.png)](https://user-images.githubusercontent.com/10241716/86878116-d5e3ad80-c11a-11ea-93bc-892ce780b50d.png)

