---
layout:     post
title:      【Android】如何限制 EditText 输入的长度与小数点位数？
subtitle:   【Android】如何限制 EditText 输入的长度与小数点位数？
date:       2019-03-08 16:02:58
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Android
    - 交易所
    - 数字货币
---

>【Android】如何限制 EditText 输入的长度与小数点位数？

# 【Android】如何限制 EditText 输入的长度与小数点位数？


最近做交易所APP发现 EditText 设置 xml 的
android:maxLength="14"

**无效！！**

 

**查了网上的方法最终确定：**
mEtPrice.setFilters(new InputFilter[]{new DecimalDigitsInputFilter(mBDPriceCount), new InputFilter.LengthFilter(14)}); //new DecimalDigitsInputFilter(mBDPriceCount) 设置几位小数 //new InputFilter.LengthFilter(14) 设置总输入几个字符

 


