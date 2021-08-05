# 【Android】如何限制 EditText 输入的长度与小数点位数？


最近做交易所APP发现 EditText 设置 xml 的
android:maxLength="14"

**无效！！**

 

**查了网上的方法最终确定：**
mEtPrice.setFilters(new InputFilter[]{new DecimalDigitsInputFilter(mBDPriceCount), new InputFilter.LengthFilter(14)}); //new DecimalDigitsInputFilter(mBDPriceCount) 设置几位小数 //new InputFilter.LengthFilter(14) 设置总输入几个字符

 


