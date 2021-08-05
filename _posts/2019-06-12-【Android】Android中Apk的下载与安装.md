---
layout:     post
title:      【Android】Android中Apk的下载与安装
subtitle:   【Android】Android中Apk的下载与安装
date:       2019-06-12 11:50:10
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Android
---

>【Android】Android中Apk的下载与安装

# 【Android】Android中Apk的下载与安装


相信很多应用都是采用内部下载的方式，这样的体验肯定比跳转到浏览器好得多！而应用商店审核周期长，无法实时更新最新应用！所以内部下载更新就显得尤为重要！

1.要美观好看，给用户实时的反馈下载情况：

界面体现为下载百分比%，下载速度 kb/s，圆环进度

2.下载完成后要自动安装：

Android6.0，需要动态申请权限，读取写入。
Android7.0，需要通过fileprovider的方式创建Uri
Android8.0，需要申请【安装未知来源应用权限】
针对第一个问题，我们采用自定义View来完成，可定制化高，样式想怎样改怎样改。而第二个问题就需要我们队权限的申请和对路径创建方式的注意了。

先来一个效果图：

![](//upload-images.jianshu.io/upload_images/1503465-0205c0b0ffe39794.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/280/format/webp)

下载安装apk.gif

 

这个其实就是简单的一个Dialog了，中间的狮子图片是应用LOGO，下面的正在下载就是一个文字描述，难点主要是中间的进度圆圈和圆圈点上的行星进度。

1.1新建一个Dialog弹出框:DownloadCircleDialog
public class DownloadCircleDialog extends Dialog { public DownloadCircleDialog(Context context) { super(context, R.style.Theme_Ios_Dialog); } DownloadCircleView circleView; TextView tvMsg; @Override protected void onCreate(Bundle savedInstanceState) { super.onCreate(savedInstanceState); setContentView(R.layout.download_circle_dialog_layout); this.setCancelable(false);//设置点击弹出框外部，无法取消对话框 circleView = findViewById(R.id.circle_view); tvMsg = findViewById(R.id.tv_msg); } public void setProgress(int progress) { circleView.setProgress(progress); circleView.postInvalidate(); } public void setMsg(String msg){ tvMsg.setText(msg); } }

在style.xml中写入样式：Theme.Ios.Dialog

<!-- IOSDialog --> <style name="Theme.Ios.Dialog" parent="@android:style/Theme.Dialog"> <!-- Dialog的windowFrame框为无 --> <!-- <item name="android:windowFrame">@null</item> --> <!-- 边框 --> <item name="android:windowIsFloating">true</item> <!-- 是否浮现在activity之上 --> <item name="android:windowIsTranslucent">true</item> <!-- 半透明 --> <item name="android:windowNoTitle">true</item> <!-- 设置dialog的背景 --> <item name="android:windowBackground">@android:color/transparent</item> <!-- 背景是否模糊显示 --> <item name="android:backgroundDimEnabled">true</item> <!-- 模糊 --> <item name="android:textColorPrimaryInverse">@android:color/black</item> </style>

layout中的布局文件:download_circle_dialog_layout

<?xml version="1.0" encoding="utf-8"?> <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android" android:id="@+id/layout_notice" android:background="@color/transparent" android:layout_width="match_parent" android:layout_height="match_parent" android:gravity="center" android:orientation="vertical" > <com.yy.trade.customview.DownloadCircleView android:id="@+id/circle_view" android:layout_centerInParent="true" android:layout_width="180dp" android:layout_height="180dp"/> <ImageView android:layout_centerInParent="true" android:src="@mipmap/icon_logo" android:layout_width="64dp" android:layout_height="64dp"/> <TextView android:id="@+id/tv_msg" android:layout_centerHorizontal="true" android:layout_below="@+id/circle_view" android:text="正在下载..." android:layout_width="wrap_content" android:layout_height="wrap_content"/> </RelativeLayout>

1.2中间下载进度圆圈：DownloadCircleView

public class DownloadCircleView extends View { Paint mBgPaint; Paint mStepPaint; Paint mTxtCirclePaint; Paint mTxtPaint; int outsideRadius=160; int progressWidth =8; float progresTtextSize = 24; Context context; public DownloadCircleView(Context context) { super(context); } public DownloadCircleView(Context context, @Nullable AttributeSet attrs) { super(context, attrs); init(context); } public DownloadCircleView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) { super(context, attrs, defStyleAttr); } @Override protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) { int width; int height; int size = MeasureSpec.getSize(widthMeasureSpec); int mode = MeasureSpec.getMode(widthMeasureSpec); if (mode == MeasureSpec.EXACTLY) { width = size; } else { width = (int) ((2 /* outsideRadius) + progressWidth); } size = MeasureSpec.getSize(heightMeasureSpec); mode = MeasureSpec.getMode(heightMeasureSpec); if (mode == MeasureSpec.EXACTLY) { height = size; } else { height = (int) ((2 /* outsideRadius) + progressWidth); } setMeasuredDimension(width, height); } private void init(Context context) { this.context = context; mBgPaint = new Paint(); mBgPaint.setStrokeWidth(8); mBgPaint.setColor(Color.GRAY); this.mBgPaint.setAntiAlias(true); //消除锯齿 this.mBgPaint.setStyle(Paint.Style.STROKE); //绘制空心圆 mStepPaint = new Paint(); mStepPaint.setStrokeWidth(8); mStepPaint.setColor(Color.parseColor("/#3B4463")); this.mStepPaint.setAntiAlias(true); //消除锯齿 this.mStepPaint.setStyle(Paint.Style.STROKE); //绘制空心圆 mTxtCirclePaint = new Paint(); mTxtCirclePaint.setColor(Color.parseColor("/#3B4463")); this.mTxtCirclePaint.setAntiAlias(true); //消除锯齿 this.mTxtCirclePaint.setStyle(Paint.Style.FILL); //绘制实心圆 mTxtPaint = new Paint(); mTxtPaint.setTextSize(progresTtextSize); mTxtPaint.setColor(Color.WHITE); this.mTxtPaint.setAntiAlias(true); //消除锯齿 this.mTxtPaint.setStyle(Paint.Style.FILL); //绘制实心圆 } float maxProgress=100f; float progress =0f; public void setProgress(float progress) { this.progress = progress; } @Override protected void onDraw(Canvas canvas) { super.onDraw(canvas); //灰色圆圈 int circlePoint = getWidth() / 2; canvas.drawCircle(circlePoint, circlePoint, outsideRadius, mBgPaint); //画出圆 //进度 RectF oval = new RectF(); oval.left=circlePoint - outsideRadius; oval.top=circlePoint - outsideRadius; oval.right=circlePoint + outsideRadius; oval.bottom=circlePoint + outsideRadius; float range = 360 /* (progress / maxProgress); canvas.drawArc(oval, -90, range, false, mStepPaint); //根据进度画圆弧 //轨道圆和文字 double x1 = circlePoint + outsideRadius /* Math.cos((range-90) /* 3.14 / 180); double y1 = circlePoint + outsideRadius /* Math.sin((range-90) /* 3.14 / 180); canvas.drawCircle((float) x1, (float) y1, outsideRadius/4, mTxtCirclePaint); String txt = (int) progress + "%"; float strwid = mTxtPaint.measureText(txt);//直接返回参数字符串所占用的宽度 canvas.drawText(txt,(float) x1-strwid/2, (float) y1+progresTtextSize/2,mTxtPaint); } }
 
这样，下载样式基本就完成了，每次通过方法setProgress和setMsg就可以去设置下载的进度和速度了！

下面说说下载：采用 **[okhttp](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fsquare%2Fokhttp)**来下载apk文件，通过 **[ProgressManager](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FJessYanCoding%2FProgressManager)**来监听进度，通过 **[easypermissions](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fgooglesamples%2Feasypermissions)**简化动态申请权限

2.1首先我们写个下载工具类：DownloadUtils
public class DownloadUtils { private static DownloadUtils instance; private OkHttpClient okHttpClient; private Handler mHandler; //所有监听器在 Handler 中被执行,所以可以保证所有监听器在主线程中被执行 public static DownloadUtils getInstance() { if (instance == null) instance = new DownloadUtils(); return instance; } private DownloadUtils() { this.mHandler = new Handler(Looper.getMainLooper()); OkHttpClient.Builder builder = new OkHttpClient.Builder(); okHttpClient = ProgressManager.getInstance().with(builder).build(); } public interface OnDownloadListener{ //*/* /* 下载成功 /*/ void onDownloadSuccess(); //*/* /* @param progress 下载进度 /*/ void onDownloading(ProgressInfo progress); //*/* /* 下载失败 /*/ void onDownloadFailed(); } //*/* /* @param url 下载连接 /* @param saveDir 储存下载文件的SDCard目录 /* @param listener 下载监听 /*/ public void download(final String url, final String saveDir, final String saveName, final OnDownloadListener listener) { Request request = new Request.Builder().url(url).build(); okHttpClient.newCall(request).enqueue(new Callback() { @Override public void onFailure(Call call, IOException e) { // 下载失败 listener.onDownloadFailed(); } @Override public void onResponse(Call call, Response response) throws IOException { // Okhttp/Retofit 下载监听 InputStream is = null; byte[] buf = new byte[2048]; int len = 0; FileOutputStream fos = null; // 储存下载文件的目录 try { is = response.body().byteStream(); File file = new File(saveDir, saveName); if (!file.getParentFile().exists()) file.getParentFile().mkdirs(); fos = new FileOutputStream(file); while ((len = is.read(buf)) != -1) { fos.write(buf, 0, len); } fos.flush(); mHandler.post(new Runnable() { @Override public void run() { // 下载完成 listener.onDownloadSuccess(); } }); } catch (Exception e) { Log.e("下载异常", e.getMessage()); listener.onDownloadFailed(); } finally { try { if (is != null) is.close(); } catch (IOException e) { } try { if (fos != null) fos.close(); } catch (IOException e) { } } } }); ProgressManager.getInstance().addResponseListener(url, new ProgressListener() { @Override public void onProgress(ProgressInfo progressInfo) { listener.onDownloading(progressInfo); } @Override public void onError(long l, Exception e) { listener.onDownloadFailed(); } }); } }

上面通过

ProgressManager.getInstance().with(builder).build();
创建的

okHttpClient
也就相当于把ProgressManager加入到了OkHttp中，这样ProgressManager监听才会有效！
然后我们在需要下载apk的页面中加入：

//*/* /* 有新版本 /*/ final static int PG_WRITE = 0X0008; @AfterPermissionGranted(PG_WRITE) private void showNewVersion() { String[] perms = {READ_EXTERNAL_STORAGE, WRITE_EXTERNAL_STORAGE}; if (!EasyPermissions.hasPermissions(this, perms)) { EasyPermissions.requestPermissions(MainActivity.this, getString(R.string.need_permiss), PG_WRITE, perms); return; } new ApkUpdateDialog(MainActivity.this, "软件更新", "下载QQ测试", new ApkUpdateDialog.OnOkListener() { @Override public void onOkClick() { String down_url = "https://qd.myapp.com/myapp/qqteam/AndroidQQ/mobileqq_android.apk"; downloadApk(MainActivity.this, down_url); } }).show(); } public void downloadApk(final Activity context, String down_url) { dialog.show(); DownloadUtils.getInstance().download(down_url, SdUtils.getDownloadPath(), "QQ.apk", new DownloadUtils.OnDownloadListener() { @Override public void onDownloadSuccess() { dialog.dismiss(); L.i("恭喜你下载成功，开始安装！==" + SdUtils.getDownloadPath() + "QQ.apk"); showToast("恭喜你下载成功，开始安装！"); String successDownloadApkPath = SdUtils.getDownloadPath() + "QQ.apk"; installApkO(MainActivity.this, successDownloadApkPath); } @Override public void onDownloading(ProgressInfo progressInfo) { dialog.setProgress(progressInfo.getPercent()); boolean finish = progressInfo.isFinish(); if (!finish) { long speed = progressInfo.getSpeed(); dialog.setMsg("(" + (speed > 0 ? FormatUtils.formatSize(context, speed) : speed) + "/s)正在下载..."); } else { dialog.setMsg("下载完成！"); } } @Override public void onDownloadFailed() { dialog.dismiss(); showToast("下载失败！"); } }); } //*/* /* 兼容8.0安装位置来源的权限 /*/ private void installApkO(Context context, String downloadApkPath) { if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) { //是否有安装位置来源的权限 boolean haveInstallPermission = getPackageManager().canRequestPackageInstalls(); if (haveInstallPermission) { L.i("8.0手机已经拥有安装未知来源应用的权限，直接安装！"); AppUtils.installApk(context, downloadApkPath); } else { new CakeResolveDialog(context, "安装应用需要打开安装未知来源应用权限，请去设置中开启权限", new CakeResolveDialog.OnOkListener() { @Override public void onOkClick() { Uri packageUri = Uri.parse("package:"+ AppUtils.getAppPackageName()); Intent intent = new Intent(Settings.ACTION_MANAGE_UNKNOWN_APP_SOURCES,packageUri); } }).show(); } } else { AppUtils.installApk(context, downloadApkPath); } } @Override public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) { super.onRequestPermissionsResult(requestCode, permissions, grantResults); EasyPermissions.onRequestPermissionsResult(requestCode, permissions, grantResults, this); } @Override public void onPermissionsGranted(int requestCode, List<String> perms) { T.showShort("已经获得此权限！"); } @Override public void onPermissionsDenied(int requestCode, List<String> perms) { L.i("申请的权限被拒绝了，提醒用户去设置"); if (EasyPermissions.somePermissionPermanentlyDenied(this, perms)) { new AppSettingsDialog.Builder(this).setTitle(R.string.need_open_relevant_power).setRationale(R.string.some_permission_must_need).build().show(); } } @Override protected void onActivityResult(int requestCode, int resultCode, Intent data) { super.onActivityResult(requestCode, resultCode, data); L.i("回调。。。requestCode==" + requestCode + " resultCode==" + resultCode); if (requestCode == AppSettingsDialog.DEFAULT_SETTINGS_REQ_CODE) { Toast.makeText(this, R.string.some_permission_must_need, Toast.LENGTH_SHORT).show(); } if (requestCode == 10086) { L.i("设置了安装未知应用后的回调。。。"); String successDownloadApkPath = SdUtils.getDownloadPath() + "QQ.apk"; installApkO(MainActivity.this, successDownloadApkPath); } }

上面代码第一段**showNewVersion**就是对读写权限的申请，**downloadApk**下载进度的监听，下载完成通过**installApkO**来安装，**installApkO**中判断如果没有安装位置来源的权限就跳转到设置开启安装位置来源权限的页面，设置完成后回到这个页面继续安装！
上面的**AppUtils.installApk**是我写的一个工具方法，

public static void installApk(Context context,String downloadApk) { Intent intent = new Intent(Intent.ACTION_VIEW); File file = new File(downloadApk); L.i("安装路径=="+downloadApk); if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) { Uri apkUri = FileProvider.getUriForFile(context, AppUtils.getAppPackageName()+".fileprovider", file); intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION); intent.setDataAndType(apkUri, "application/vnd.android.package-archive"); } else { intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK); Uri uri = Uri.fromFile(file); intent.setDataAndType(uri, "application/vnd.android.package-archive"); } context.startActivity(intent); }

判断如果>=7.0就通过**fileprovider**来创建Uri,避免安装出现解析包异常！6.0的读写权限通过**showNewVersion（）**方法进行了申明，8.0的安装未知来源应用权限在**installApkO**进行了判断申请，从而使安装APK兼容了6，7，8！9.0的机子还没用过，不过如果没改动，应该也可以安装！

这样一个apk从开始下载，进度显示到安装就完成了！说起来就是一个apk的下载安装，但是其实代码量和坑还是挺多的：

**坑一**：最开始没有使用ProgressManager来进度监听，而是在download方法的写文件中监听下载进度：

public void download(final String url, final String saveDir, final OnDownloadListener listener) { Request request = new Request.Builder().url(url).build(); okHttpClient.newCall(request).enqueue(new Callback() { @Override public void onFailure(Call call, IOException e) { // 下载失败 listener.onDownloadFailed(); } @Override public void onResponse(Call call, Response response) throws IOException { InputStream is = null; byte[] buf = new byte[2048]; int len = 0; FileOutputStream fos = null; // 储存下载文件的目录 String savePath = isExistDir(saveDir); try { is = response.body().byteStream(); long total = response.body().contentLength(); File file = new File(savePath, getNameFromUrl(url)); fos = new FileOutputStream(file); long sum = 0; while ((len = is.read(buf)) != -1) { fos.write(buf, 0, len); sum += len; int progress = (int) (sum /* 1.0f / total /* 100); // 下载中 listener.onDownloading(progress); } fos.flush(); // 下载完成 listener.onDownloadSuccess(); } catch (Exception e) { listener.onDownloadFailed(); } finally { try { if (is != null) is.close(); } catch (IOException e) { } try { if (fos != null) fos.close(); } catch (IOException e) { } } } }); }

这样监听到的下载进度并不是真的下载进度，而是下载文件后写入到手机的速度，体现到界面就是最开始下载进度是0%一直不动，然后2秒钟就从0%转圈到100%，就下载完成了，给用户感觉一点都不真实！

**坑二**：下载完成后解析包错误：

a.主要原因就是没有使用

Uri apkUri = FileProvider.getUriForFile(context, AppUtils.getAppPackageName()+".fileprovider", file);
的方式来创建Uri 安装，
b.还有就是因为文件名字不正确，最开始我的download方法中没有saveName方法，而是通过下载地址截取最后的“/”来写入文件名的，但是有的下载地址并不是以apk结尾，从而导致解析包错误！
c.还有就是根本没有这个文件路径，从而导致写错误，所以在download方法中写入本地文件前我加入了如果没有文件路径就先创建当前路径
File file = new File(saveDir, saveName); if (!file.getParentFile().exists()) file.getParentFile().mkdirs();

**坑三**：下载出错：

CertPathValidatorException: Trust anchor for certification path not found
，（上面的代码没有加入，因为每个人的OkHttpClient都不同，我是写了个工具类来创建的OkHttpClient，所以工具类中加入了进度读取和跳过SSL验证的，由于自私原因，大家自己加吧。）
相信有的应用是放在自己的服务器的，而又有https，但很多都是没有证书的，导致下载不了！所以我们就需要Okhttp绕过证书验证，参考：
[https://blog.csdn.net/O0mm0O/article/details/76686917](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2FO0mm0O%2Farticle%2Fdetails%2F76686917)
**坑四**：无法安装：
Android8.0需要安装权限：

<uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES"/>
,同时7.0以上还需要安装未知来源的权限，不然也无法安装，可以参考：
[https://www.jianshu.com/p/a6209440a518](https://www.jianshu.com/p/a6209440a518)
**坑五**：无法安装：由于配置xml中的provider_paths.xml只写了

<?xml version="1.0" encoding="utf-8"?> <paths xmlns:android="http://schemas.android.com/apk/res/android"> <external-path name="images" path="test/"/> </paths>

导致无法读取路径而无法安装，因为我们是下载到Download的所以还要加入：

<?xml version="1.0" encoding="utf-8"?> <paths xmlns:android="http://schemas.android.com/apk/res/android"> <external-path name="images" path="test/"/> <external-path name="download" path="Download/"/> </paths>

这样才算完整！关于fileprovider路径介绍：
[https://blog.csdn.net/leilifengxingmw/article/details/57405908](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2Fleilifengxingmw%2Farticle%2Fdetails%2F57405908)
**坑六**：安装完成后闪退，或安装完成后点击打开闪退
在大部分手机上没有问题，但在Vivo X9上居然安装完成后闪退了，我觉得这应该是应用已经死了，所有安装完成后立即启动有问题，而其他手机就没有问题，我觉得还是Vivo手机的厂商定制问题！所以解决办法就是安装的时候启动一个新的任务栈来安装：

public static void installApk(Context context,String downloadApk) { Intent intent = new Intent(Intent.ACTION_VIEW); File file = new File(downloadApk); L.i("安装路径=="+downloadApk); if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) { Uri apkUri = FileProvider.getUriForFile(context, AppUtils.getAppPackageName()+".fileprovider", file); intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK); intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION); intent.setDataAndType(apkUri, "application/vnd.android.package-archive"); } else { intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK); Uri uri = Uri.fromFile(file); intent.setDataAndType(uri, "application/vnd.android.package-archive"); } context.startActivity(intent); }

其中：

intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
就是关键啦！

下个应用如果还需要下载，我就把上面的代码复制进去，免得每次都要去找写了下载安装APK代码的项目，然后一部分一部分去查找复制！这么多代码记肯定是记不住的，这辈子都不可能记住的，所以写这里方便下次Copy!

SdUtils由于私发不出来,写在这里：
package cn.com.smallcake_utils; import android.content.Context; import android.database.Cursor; import android.net.Uri; import android.os.Environment; import android.provider.MediaStore; import java.io.File; import java.math.BigDecimal; //*/* /* MyApplication -- cn.com.smallcake_utils /* Created by Small Cake on 2017/8/17 11:18. /* sd卡路径获取工具类 /*/ public class SdUtils { //*/* /* sd卡获取根目录路径 /* @return /storage/emulated/0/ /*/ public static String getRootPath() { return Environment.getExternalStorageDirectory()+ File.separator; } //*/* /* sd卡获取下载路径 /* @return /storage/emulated/0/Download/ /*/ public static String getDownloadPath() { return Environment.getExternalStorageDirectory()+ File.separator + Environment.DIRECTORY_DOWNLOADS+ File.separator; } //*/* /* 相机存放图片路径 /* @return /storage/emulated/0/DCIM/Camera/ /*/ public static String getCameraPath(){ return Environment.getExternalStorageDirectory() + File.separator + Environment.DIRECTORY_DCIM + File.separator + "Camera" + File.separator; } public static String getCropImgPath(){ String path = getQiDianFileDir() + File.separator+"imgCrop"+File.separator; FileUtils.makeDirs(path); return path; } public static String getIMRecordPath(){ String path = getQiDianFileDir() + File.separator+"mic"+File.separator; FileUtils.makeDirs(path); return path; } public static String getQiDianFileDir(){ String path = Environment.getExternalStorageDirectory() + File.separator + "QidianYule"; FileUtils.makeDirs(path); return path; } //*/* /* Uri photoUri = data.getData(); /* 根据相册返回的Uri获取照片路径 /* @return /*/ public static String getResultPhotoPath(Context activity, Uri photoUri){ String[] filePathColumn={MediaStore.Audio.Media.DATA}; Cursor cursor=activity.getContentResolver().query(photoUri,filePathColumn,null,null,null); cursor.moveToFirst(); String photoPath=cursor.getString(cursor.getColumnIndex(filePathColumn[0])); cursor.close(); return photoPath; } //*/* /* 获取应用缓存路径 /* @return /*/ public static String getCachePath() { return SmallUtils.getApp().getCacheDir().getPath()+ File.separator; } //*/* /* 清除缓存 /* @param context /*/ public static void clearAllCache(Context context) { deleteDir(context.getCacheDir()); if (Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)) { deleteDir(context.getExternalCacheDir()); } } //*/* /* 获取缓存大小 /* @param context /* @return /* @throws Exception /*/ public static String getTotalCacheSize(Context context) throws Exception { long cacheSize = getFolderSize(context.getCacheDir()); if (Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)) { cacheSize += getFolderSize(context.getExternalCacheDir()); } cacheSize += getFolderSize(new File(Environment.getExternalStorageDirectory() + File.separator + "QidianYule")); return getFormatSize(cacheSize); } // 获取文件大小 //Context.getExternalFilesDir() --> SDCard/Android/data/你的应用的包名/files/ 目录，一般放一些长时间保存的数据 //Context.getExternalCacheDir() --> SDCard/Android/data/你的应用包名/cache/目录，一般存放临时缓存数据 private static long getFolderSize(File file) throws Exception { long size = 0; try { File[] fileList = file.listFiles(); for (int i = 0; i < fileList.length; i++) { // 如果下面还有文件 if (fileList[i].isDirectory()) { size = size + getFolderSize(fileList[i]); } else { size = size + fileList[i].length(); } } } catch (Exception e) { e.printStackTrace(); } return size; } private static String getFormatSize(double size) { double kiloByte = size / 1024; if (kiloByte < 1) { // return size + "Byte"; return "0K"; } double megaByte = kiloByte / 1024; if (megaByte < 1) { BigDecimal result1 = new BigDecimal(Double.toString(kiloByte)); return result1.setScale(2, BigDecimal.ROUND_HALF_UP) .toPlainString() + "K"; } double gigaByte = megaByte / 1024; if (gigaByte < 1) { BigDecimal result2 = new BigDecimal(Double.toString(megaByte)); return result2.setScale(2, BigDecimal.ROUND_HALF_UP) .toPlainString() + "M"; } double teraBytes = gigaByte / 1024; if (teraBytes < 1) { BigDecimal result3 = new BigDecimal(Double.toString(gigaByte)); return result3.setScale(2, BigDecimal.ROUND_HALF_UP) .toPlainString() + "GB"; } BigDecimal result4 = new BigDecimal(teraBytes); return result4.setScale(2, BigDecimal.ROUND_HALF_UP).toPlainString() + "TB"; } //*/* /* 删除文件夹及文件 /* @param dir /* @return /*/ public static boolean deleteDir(File dir) { if (dir != null && dir.isDirectory()) { String[] children = dir.list(); if(children!=null) { for (int i = 0; i < children.length; i++) { boolean success = deleteDir(new File(dir, children[i])); if (!success) { return false; } } } } return dir.delete(); } }

 


