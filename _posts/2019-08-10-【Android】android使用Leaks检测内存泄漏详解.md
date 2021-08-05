# 【Android】android使用Leaks检测内存泄漏详解


# Leaks 内存泄漏检测工具使用

网址:[https://github.com/square/leakcanary](https://github.com/square/leakcanary) 
在你的module中添加依赖
debugCompile 'com.squareup.leakcanary:leakcanary-android:1.4-beta2' releaseCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.4-beta2' testCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.4-beta2'

在Application中添加

LeakCanary.install(this);

并且确保你的app处于调试模式,如下图 
 ![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20160606142343624)
当切换到release版本的时候,leakcanary-debug不会被打包.所以切换到release之后不用对leakcanary做注释或者删除等操作. 
现在就可以开始使用了,重新编译你的工程,运行在模拟器或真机上. 
在各个页面中测试,如果存在内存泄漏的情况,leaks会弹出通知提醒你查看. 
在leaks中会有详细的泄漏说明，如产生内存泄漏的大小，产生内存泄漏的页面和相关的信息。 

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20160822143933833)

内存泄漏详情： 

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20160822144000095)
最后有一些关于内存泄漏的建议: 
为了避免上下文相关的内存泄漏,记住以下几点: 
不要长期引用context-activity(引用一个活动应该有相同的生命周期活动本身) 
使用getApplicationContext而不是context或activity试试 
避免非静态内部类的一个活动如果你不控制自己的生命周期,使用静态内部类,让一个弱引用来传递,并且记住,垃圾收集器对于内存泄漏并不保险。

下面说几个作者遇到的内存泄漏的情况以及解决方案: 
使用百度地图LocationClient.在Activity被销毁时调用
if (mLocClient != null){ mLocClient.unRegisterLocationListener(myListener); mLocClient.stop(); }

使用友盟分享登录时, 传递getApplicationContext

第三方库如果需要传入Activity的时候，传递一个弱引用进去， 可以避免内存泄漏,如下：
Reference<HomeActivity> reference = new WeakReference(getActivity()); new ShareAction(reference.get()).setDisplayList(displaylist);

在Handler中容易造成内存泄漏，最好使用弱引用方式，要么，在Activity销毁的时候清空所有的handler栈。

在使用WebView的时候也可能出现内存泄漏，解决办法，我的另一篇博客里有详细讲解。 
http://blog.csdn.net/fancy_xty/article/details/51595697

在使用RxJava的时候也可能会出现内存泄漏，可以使用RxLifecycler来解决。
 

