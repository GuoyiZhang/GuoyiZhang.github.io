---
layout:     post
title:      【Android】Android实现切换主题皮肤功能
subtitle:   【Android】Android实现切换主题皮肤功能
date:       2019-05-14 14:21:40
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Android
---
>blog.getTitle() 

# 【Android】Android实现切换主题皮肤功能


简介
通过观察许多主流的app可以发现都具有换肤功能，可以根据用户的喜欢定制自己的界面，比如微博、淘宝、网易新闻等，它们的皮肤（主题）大多数都是要通过下载然后再替换。替换的是什么呢？当然就是资源文件啦，再简单一点说，就是去替换界面上的字体、颜色、背景、图片这些东西。

正文

。首先我们先来做个简单的一键切换主题的功能查看代码如下：
<resources> <!-- Base application theme. --> <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar"> <!-- Customize your theme here. --> <item name="colorPrimary">@color/colorPrimary</item> <item name="colorPrimaryDark">@color/colorPrimaryDark</item> <item name="colorAccent">@color/colorAccent</item> </style> <style name="lightTheme"> <item name="android:textColor">/#eeeccc</item> <item name="android:background">/#ffffff</item> </style> <style name="blackTheme"> <item name="android:textColor">/#ffffff</item> <item name="android:background">/#000000</item> </style> </resources>
 
package com.tuyasmart.tv.changetheme; import android.support.v7.app.AppCompatActivity; import android.os.Bundle; import android.view.View; public class MainActivity extends AppCompatActivity { private boolean isClick; SharedPreferencesUtil sharedPreferencesUtil; @Override protected void onCreate(Bundle savedInstanceState) { sharedPreferencesUtil = new SharedPreferencesUtil(this, "sp"); int theme = sharedPreferencesUtil.getIntValue("theme", 1); if (theme == 1){ setTheme(R.style.lightTheme); }else{ setTheme(R.style.blackTheme); } super.onCreate(savedInstanceState); setContentView(R.layout.activity_main); findViewById(R.id.tv_btn).setOnClickListener(new View.OnClickListener() { @Override public void onClick(View view) { if (isClick) { isClick = false; sharedPreferencesUtil.putIntValue("theme",1); } else { isClick = true; sharedPreferencesUtil.putIntValue("theme",2); } } }); } }

但是这种情况下呢，一键切换皮肤，应用程序需要退出，再打开才显示效果。同时，如果我们要是有多种主题的话，用这种方法，应用程序就会变得很大。那我们就得想，皮肤能不能通过网络下载到本地，然后一键替换就可以了？答案是可以的。

这个方案的思路：把主题包做成一个APK，注意这个APK是不能在桌面上显示的，只能在设置中的应用程序列表里查询到然后在你的活动里取这个APK的资源。

先看资源包的应用程序代码：

style.xml
<resources> <!-- Base application theme. --> <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar"> <!-- Customize your theme here. --> <item name="colorPrimary">@color/colorPrimary</item> <item name="colorPrimaryDark">@color/colorPrimaryDark</item> <item name="colorAccent">@color/colorAccent</item> <item name="android:textColor">/#ffffff</item> <item name="android:background">/#000000</item> </style> </resources>

的AndroidManifest.xml文件

<?xml version="1.0" encoding="utf-8"?> <manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.tuyasmart.tv.childtheme"> <application android:allowBackup="true" android:icon="@mipmap/ic_launcher" android:label="@string/app_name" android:roundIcon="@mipmap/ic_launcher_round" android:supportsRtl="true" android:theme="@style/AppTheme"> <activity android:name=".MainActivity"> <action android:name="android.intent.action.MAIN" /> </activity> </application> </manifest>

这里需要注意的是，我们把<category android：name =“android.intent.category.LAUNCHER”/>去掉了，就是为了确保app安装之后，不会在桌面上显示。

至此，只要把childTheme的APK安装到手机上，就可以动态加载主题了。但是这种方式也是不完美的，因为setTheme方法是要重启应用程序之后才生效。体验上就不是很友好了，因为用户重启应用程序的成本是比较高的。那我们有什么办法能解决这个问题呢？

首先我们知道的是应用程序里面只有一个活动会做主题切换的功能，其他活性是不需要实现这个功能的。也就是说只要这个活性做了主题切换功能，其他活性只要是新启动的话，都会走在OnCreate ，这样主题也就切换过去了这样一想，只要我们能实现一个活动不重启活动就能实现切换皮肤的功能就OK了代码如下。：

首先我们先定义自定义属性：
<resources> <attr name="my_background" format="reference|color"/> <attr name="my_textcolor" format="reference|color"/> </resources>

再定义主题

<style name="lightTheme"> <item name="my_textcolor">/#eeeccc</item> <item name="my_background">/#ffffff</item> </style> <style name="blackTheme"> <item name="my_textcolor">/#ffffff</item> <item name="my_background">/#000000</item> </style>

再写布局文件

<?xml version="1.0" encoding="utf-8"?> <com.tuyasmart.tv.changetheme.ThemeUIReleativeLayout xmlns:android="http://schemas.android.com/apk/res/android" xmlns:app="http://schemas.android.com/apk/res-auto" xmlns:tools="http://schemas.android.com/tools" android:id="@+id/main_view" android:layout_width="match_parent" android:layout_height="match_parent" android:background="?attr/my_background" tools:context=".MainActivity"> <com.tuyasmart.tv.changetheme.ThemeTextView android:id="@+id/tv_btn" android:layout_width="wrap_content" android:layout_height="wrap_content" android:layout_centerInParent="true" android:background="?attr/my_background" android:text="change theme on click!" android:textColor="?attr/my_textcolor" /> </com.tuyasmart.tv.changetheme.ThemeUIReleativeLayout>

主要活动

package com.tuyasmart.tv.changetheme; import android.content.Context; import android.content.pm.PackageManager; import android.content.res.Resources; import android.support.v7.app.AppCompatActivity; import android.os.Bundle; import android.view.View; import android.view.ViewGroup; public class MainActivity extends AppCompatActivity { private boolean isClick; SharedPreferencesUtil sharedPreferencesUtil; @Override protected void onCreate(Bundle savedInstanceState) { setTheme(R.style.lightTheme); sharedPreferencesUtil = new SharedPreferencesUtil(this, "sp"); super.onCreate(savedInstanceState); setContentView(R.layout.activity_main); final ThemeUIReleativeLayout mainView = findViewById(R.id.main_view); findViewById(R.id.tv_btn).setOnClickListener(new View.OnClickListener() { @Override public void onClick(View view) { if (isClick) { isClick = false; sharedPreferencesUtil.putIntValue("theme", 1); } else { isClick = true; sharedPreferencesUtil.putIntValue("theme", 2); } int theme = sharedPreferencesUtil.getIntValue("theme", 1); if (theme == 1) { setTheme(R.style.lightTheme); } else { //获取子app的style中的Apptheme，动态修改我们的主题。 int resourceId = getResourceId(getPackageContext(MainActivity.this , "com.tuyasmart.tv.childtheme"), "style", "AppTheme"); // setTheme(resourceId); setTheme(R.style.blackTheme); } changeTheme(mainView, getTheme()); } }); } //*/* /* 获取其他apk的context /* /* @param context /* @param packageName /* @return /*/ public static Context getPackageContext(Context context, String packageName) { try { return context.createPackageContext(packageName, Context.CONTEXT_INCLUDE_CODE | Context.CONTEXT_IGNORE_SECURITY); } catch (PackageManager.NameNotFoundException e) { e.printStackTrace(); } return null; } //*/* /* 获取指定type里面的name属性 /* /* @param context /* @param type /* @param name /* @return /*/ public static int getResourceId(Context context, String type, String name) { return context.getResources().getIdentifier(name, type, context.getPackageName()); } public static void changeTheme(View rootView, Resources.Theme theme) { //递归调用changeTheme-----递归调用setTheme了。 if (rootView instanceof IThemeUI) { ((IThemeUI) rootView).setTheme(theme); if (rootView instanceof ViewGroup) { int count = ((ViewGroup) rootView).getChildCount(); for (int i = 0; i < count; i++) { changeTheme(((ViewGroup) rootView).getChildAt(i), theme); } } } } }

至此，皮肤就可以动态修改了通过动态下载的皮肤也是同样的道理使用可以看如下代码。：

childTheme：

添加attrs.xml
<?xml version="1.0" encoding="utf-8"?> <resources> <attr name="my_background" format="reference|color"/> <attr name="my_textcolor" format="reference|color"/> </resources>

Style.xml

<resources> <!-- Base application theme. --> <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar"> <!-- Customize your theme here. --> <item name="colorPrimary">@color/colorPrimary</item> <item name="colorPrimaryDark">@color/colorPrimaryDark</item> <item name="colorAccent">@color/colorAccent</item> </style> <style name="blackTheme"> <item name="my_textcolor">/#ffffff</item> <item name="my_background">/#000000</item> </style> </resources>
 
findViewById(R.id.tv_btn).setOnClickListener(new View.OnClickListener() { @Override public void onClick(View view) { if (isClick) { isClick = false; sharedPreferencesUtil.putIntValue("theme", 1); } else { isClick = true; sharedPreferencesUtil.putIntValue("theme", 2); } int theme = sharedPreferencesUtil.getIntValue("theme", 1); if (theme == 1) { setTheme(R.style.lightTheme); } else { //获取子app的style中的Apptheme，动态修改我们的主题。 int resourceId = getResourceId(getPackageContext(MainActivity.this , "com.tuyasmart.tv.childtheme"), "style", "blackTheme"); setTheme(resourceId); // setTheme(R.style.blackTheme); } changeTheme(mainView, getTheme()); } });

 

总结

皮肤替换就暂时说到这里，如果你的皮肤切换比较简单的话，就可以直接写在应用程序里打包出去。如果皮肤种类过多的话，那就可以通过网络下载的模式进行替换。

