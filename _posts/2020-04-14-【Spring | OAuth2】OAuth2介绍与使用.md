---
layout:     post
title:      【Spring | OAuth2】OAuth2介绍与使用
subtitle:   【Spring | OAuth2】OAuth2介绍与使用
date:       2020-04-14 13:57:09
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Java
    - 后端
---

>【Spring | OAuth2】OAuth2介绍与使用

# 【Spring | OAuth2】OAuth2介绍与使用


## 什么是OAuth2

OAuth（Open Authorization，开放授权）是为用户资源的授权定义了一个安全、开放及简单的标准，第三方无需知道用户的账号及密码，就可获取到用户的授权信息
OAuth2.0是OAuth协议的延续版本，但不向后兼容OAuth 1.0即完全废止了OAuth1.0

## 应用场景

第三方应用授权登录：在APP或者网页接入一些第三方应用时，时常会需要用户登录另一个合作平台，比如QQ，微博，微信的授权登录,第三方应用通过oauth2方式获取用户信息

## 运作流程

微信开发文档流程说明如下:
1. 第三方发起微信授权登录请求，微信用户允许授权第三方应用后，微信会拉起应用或重定向到第三方网站，并且带上授权临时票据code参数； 2. 通过code参数加上AppID和AppSecret等，通过API换取access_token； 3. 通过access_token进行接口调用，获取用户基本数据资源或帮助用户实现基本操作。

具体的实现流程图如下:

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODk4MjQ4Ny0zZjk5YWQ5MDMwZmEwYjViLnBuZz9pbWFnZU1vZ3IyL2F1dG8tb3JpZW50L3N0cmlwfGltYWdlVmlldzIvMi93LzEyMDAvZm9ybWF0L3dlYnA?x-oss-process=image/format,png)

OAuth2流程图

## 步骤说明(以微信授权登录为例)

1.用户访问第三方网站,第三方应用需要用户登录验证,用户选择微信授权登录
2.第三方应用发起微信登录授权请求
https://open.weixin.qq.com/connect/oauth2/authorize?appid=APPID&redirect_uri=REDIRECT_URI&response_type=code&scope=SCOPE&state=STATE/#wechat_redirect
 参数是否必须说明appid是公众号的唯一标识redirect_uri是授权后重定向的回调链接地址response_type是返回类型，请填写codescope是应用授权作用域，snsapi_base 、snsapi_userinfostate否重定向后会带上state参数,该值会被微信原样返回，我们可以将其进行比对防止攻击或者做用户步骤1访问url保存wechat_redirect否直接在微信打开链接，可以不填此参数。做页面302重定向时候，必须带此参数

3.微信服务器拉起用户授权确认页面
4.用户授权通过
5.微信发送请求到第三方应用redirctUrl(第2步填写redirct_uri参数),返回凭证code与state(第2步自定义)

http://host/redirct_uri?code=0217a07e9c194dbf539c45c266b2dcfZ&state=state

6.第三方应用获取到code之后,根据code获取accessToken

https://api.weixin.qq.com/sns/oauth2/access_token?appid=APPID&secret=SECRET&code=CODE&grant_type=authorization_code
 参数是否必须说明appid是应用唯一标识，在微信开放平台提交应用审核通过后获得secret是应用密钥AppSecret，在微信开放平台提交应用审核通过后获得code是填写第一步获取的code参数grant_type是填authorization_code

返回参数

参数说明access_token接口调用凭证expires_inaccess_token接口调用凭证超时时间，单位（秒）refresh_token用户刷新access_tokenopenid授权用户唯一标识scope用户授权的作用域，使用逗号（,）分隔

7.根据accessToken获取用户信息

https://api.weixin.qq.com/sns/userinfo?access_token=ACCESS_TOKEN&openid=OPENID

返回参数

参数说明openid普通用户的标识，对当前开发者帐号唯一nickname普通用户昵称sex普通用户性别，1为男性，2为女性province普通用户个人资料填写的省份city普通用户个人资料填写的城市country国家，如中国为CNheadimgurl用户头像，最后一个数值代表正方形头像大小（有0、46、64、96、132数值可选，0代表640/*640正方形头像），用户没有头像时该项为空privilege用户特权信息，json数组，如微信沃卡用户为（chinaunicom）unionid用户统一标识。针对一个微信开放平台帐号下的应用，同一用户的unionid是唯一的。

8.对用户信息进行处理(用户是否第一次登录,保存用户信息,自定义token,session处理等)
9.返回结果(步骤1对应url或者重定向到首页)

至此,微信OAuth2授权登录过程结束
具体细节请参考[微信开发平台-移动应用授权登录](https://links.jianshu.com/go?to=https%3A%2F%2Fopen.weixin.qq.com%2Fcgi-bin%2Fshowdocument%3Faction%3Ddir_list%26t%3Dresource%2Fres_list%26verify%3D1%26id%3Dopen1419317851%26token%3D%26lang%3Dzh_CN)

