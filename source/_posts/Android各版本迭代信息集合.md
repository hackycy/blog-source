---
title: Android各版本迭代信息集合
date: 2020-12-22 14:29:43
tags:
    - Android
categories:
    - Android
---

# 前言

今天分享的面试题是：

Android在版本迭代中，总会进行很多改动，那么你熟知各版本都改动了什么内容？又要怎么适配呢？

<!-- more -->

# Android4.4

- 发布`ART`虚拟机，提供选项可以开启。
- `HttpURLConnection`的底层实现改为了OkHttp。

# Android5.0

- `ART`成为默认虚拟机，完全代替Dalvik虚拟机。
- `Context.bindService()` 方法需要显式 Intent，如果提供隐式 intent，将引发异常。

# Android6.0

- 增加运行时权限限制

如果你的应用使用到了危险权限，比如在运行时进行检查和请求权限。`checkSelfPermission()`方法用于检查权限，`requestPermissions()` 方法用于请求权限。

- 取消支持Apache HTTP

Android 6.0 版移除了对 `Apache HTTP`相关类库的支持。要继续使用 Apache HTTP API，您必须先在 build.gradle 文件中声明以下编译时依赖项：

```
android {useLibrary 'org.apache.http.legacy'}
```

有的小伙伴可能不熟悉这是啥，简单说下：

> Apache HttpClient 是Apache开源组织提供的一个开源的项目,它是一个简单的HTTP客户端（并不是浏览器），可以发送HTTP请求，接受HTTP响应。

所以说白了，其实就是一个请求网络的项目框架。

# Android 7.0

- Android 7.0 引入一项新的应用签名方案 APK Signature Scheme v2
- Toast导致的BadTokenException
- 在Android7.0系统上，Android 框架强制执行了 StrictMode API 政策禁止向你的应用外公开 file:// URI。如果一项包含文件 file:// URI类型 的 Intent 离开你的应用，应用失败，并出现 `FileUriExposedException` 异常，如调用系统相机拍照录制视频，或裁切照片。

这一点其实就是限制了在应用间共享文件，如果需要在应用间共享，需要授予要访问的URI临时访问权限，我们要做的就是注册`FileProvider`：

1）声明FileProvider。

```xml
<provider
    android:name="android.support.v4.content.FileProvider"
    android:authorities="app的包名.fileProvider"
    android:grantUriPermissions="true"
    android:exported="false">
    <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/file_paths" />
</provider>
<!--androidx版本类路径为：androidx.core.content.FileProvider-->
```

2）编写xml文件，确定可访问的目录

```xml
<paths xmlns:android="http://schemas.android.com/apk/res/android">
 //代表设备的根目录new File("/");
    <root-path name="root" path="." /> 
    //context.getFilesDir()
    <files-path name="files" path="." /> 
    //context.getCacheDir()
    <cache-path name="cache" path="." /> 
    //Environment.getExternalStorageDirectory()
    <external-path name="external" path="." />
    //context.getExternalFilesDirs()
    <external-files-path name="name" path="path" />
    //getExternalCacheDirs()
     <external-cache-path name="name" path="path" />
</paths>
```

3）使用FileProvider

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
    Uri uri = FileProvider.getUriForFile(CameraActivity.this, "app的包名.fileProvider", photoFile);
} else {
    Uri uri = Uri.fromFile(photoFile);
}
```

# Android8.0

- 修改运行时权限错误

在 `Android 8.0` 之前，如果应用在运行时请求权限并且被授予该权限，系统会错误地将属于同一权限组并且在清单中注册的其他权限也一起授予应用。对于针对 Android 8.0 的应用，系统只会授予应用明确请求的权限。然而，一旦用户为应用授予某个权限，则所有后续对该权限组中权限的请求都将被自动批准。

也就是说，以前你申请了`READ_EXTERNAL_STORAGE`权限，应用会同时给你授予同权限组的`WRITE_EXTERNAL_STORAGE`权限。如果Android8.0以上，只会给你授予你请求的`READ_EXTERNAL_STORAGE`权限。如果需要`WRITE_EXTERNAL_STORAGE`权限，还要单独申请，不过系统会立即授予，不会提示。

- 修改通知

Android 8.0 对于通知修改了很多，比如通知渠道、通知标志、通知超时、背景颜色。其中比较重要的就是通知渠道，其允许您为要显示的每种通知类型创建用户可自定义的渠道。

这样的好处就是对于某个应用可以把权限分成很多类，用户来控制是否显示哪些类别的通知。而开发者要做的就是必须设置这个渠道id，否则通知可能会失效。

```java
private void createNotificationChannel() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {

            NotificationManager notificationManager = (NotificationManager)
                    getSystemService(Context.NOTIFICATION_SERVICE);

            //分组（可选）
            //groupId要唯一
            String groupId = "group_001";
            NotificationChannelGroup group = new NotificationChannelGroup(groupId, "广告");

            //创建group
            notificationManager.createNotificationChannelGroup(group);

            //channelId要唯一
            String channelId = "channel_001";

            NotificationChannel adChannel = new NotificationChannel(channelId,
                    "推广信息", NotificationManager.IMPORTANCE_DEFAULT);
            //补充channel的含义（可选）
            adChannel.setDescription("推广信息");
            //将渠道添加进组（先创建组才能添加）
            adChannel.setGroup(groupId);
            //创建channel
            notificationManager.createNotificationChannel(adChannel);

   //创建通知时，标记你的渠道id
            Notification notification = new Notification.Builder(MainActivity.this, channelId)
                    .setSmallIcon(R.mipmap.ic_launcher)
                    .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.mipmap.ic_launcher))
                    .setContentTitle("一条新通知")
                    .setContentText("这是一条测试消息")
                    .setAutoCancel(true)
                    .build();
            notificationManager.notify(1, notification);

        }
    }
```

- 悬浮窗

Android8.0以上必须使用新的窗口类型(`TYPE_APPLICATION_OVERLAY`)才能显示提醒悬浮窗：

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
    mWindowParams.type = WindowManager.LayoutParams.TYPE_APPLICATION_OVERLAY
}else {
    mWindowParams.type = WindowManager.LayoutParams.TYPE_SYSTEM_ALERT
}
```

- 不允许安装未知来源的应用

Android 8.0去除了“允许未知来源”选项，所以如果我们的App有安装App的功能（检查更新之类的），那么会无法正常安装。

```java
// <uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES"/>

private void installAPK(){

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            boolean hasInstallPermission = getPackageManager().canRequestPackageInstalls();
            if (hasInstallPermission) {
                //安装应用
            } else {
                //跳转至“安装未知应用”权限界面，引导用户开启权限
                Uri selfPackageUri = Uri.parse("package:" + this.getPackageName());
                Intent intent = new Intent(Settings.ACTION_MANAGE_UNKNOWN_APP_SOURCES, selfPackageUri);
                startActivityForResult(intent, 100);
            }
        }else {
            //安装应用
        }

    }

    //接收“安装未知应用”权限的开启结果
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == 100) {
            installAPK();
        }
    }
```

- Only fullscreen opaque activities can request orientation

只有全屏不透明的`activity`才可以设置方向。这应该是个bug，在Android8.0中出现，8.1中被修复。

我们的处理办法就是要么去掉设置方向的代码，要么舍弃透明效果。

# Android9.0

- 在9.0中默认情况下启用网络传输层安全协议 (TLS)，默认情况下已停用明文支持。也就是不允许使用http请求，要求使用`https`。解决办法就是添加网络安全配置：

```xml
<application android:networkSecurityConfig="@xml/network_security_config">

<network-security-config>
 <base-config cleartextTrafficPermitted="true" />
</network-security-config>


<!--或者在AndroidManifest.xml中配置：
android:usesCleartextTraffic="true"
-->
```

- 移除Apache HTTP 客户端

在6.0中取消了对`Apache HTTP` 客户端的支持，Android9.0中直接移除了该库，要使用的话需要添加配置：

```xml
<uses-library android:name="org.apache.http.legacy" android:required="false"/>
```

- 前台服务调用

Android 9.0 要求创建一个前台服务需要请求 FOREGROUND_SERVICE 权限，否则系统会引发 SecurityException。

```java
// <uses-permission android:name="android.permission.FOREGROUND_SERVICE" />

if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.O) {
    startForegroundService(intentService);
} else {
    startService(intentService);
}
```

- 不能在非Acitivity环境中启动Activity

在9.0 中，不能直接非 Activity 环境中（比如Service，Application）启动 Activity，否则会崩溃报错，解决办法就是加上`FLAG_ACTIVITY_NEW_TASK`

```java
Intent intent = new Intent(this, TestActivity.class);
intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
startActivity(intent);
```

# Android10

- 分区存储

Android10中默认开启了分区存储，也就是沙盒模式。应用只能看到本应用专有的目录（通过 `Context.getExternalFilesDir()` 访问）以及特定类型的媒体。

如果需要关闭这个功能可以配置：

```xml
android:requestLegacyExternalStorage="true"
```

分区存储下，访问文件的方法：

1）应用专属目录

```kotlin
//分区存储空间
val file = File(context.filesDir, filename)

//应用专属外部存储空间
val appSpecificExternalDir = File(context.getExternalFilesDir(), filename)
```

2）访问公共媒体目录文件

```kotlin
val cursor = contentResolver.query(MediaStore.Images.Media.EXTERNAL_CONTENT_URI, null, null, null, "${MediaStore.MediaColumns.DATE_ADDED} desc")
if (cursor != null) {
    while (cursor.moveToNext()) {
        val id = cursor.getLong(cursor.getColumnIndexOrThrow(MediaStore.MediaColumns._ID))
        val uri = ContentUris.withAppendedId(MediaStore.Images.Media.EXTERNAL_CONTENT_URI, id)
        println("image uri is $uri")
    }
    cursor.close()
}
```

3）SAF(存储访问框架--Storage Access Framework)

```kotlin
    val intent = Intent(Intent.ACTION_OPEN_DOCUMENT)
    intent.addCategory(Intent.CATEGORY_OPENABLE)
    intent.type = "image/*"
    startActivityForResult(intent, 100)

    @RequiresApi(Build.VERSION_CODES.KITKAT)
    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)
        if (data == null || resultCode != Activity.RESULT_OK) return
        if (requestCode == 100) {
            val uri = data.data
            println("image uri is $uri")
        }
    }
```

- 权限再次升级

从Android10开始普通应用不再允许请求权限android.permission.READ_PHONE_STATE。而且，无论你的App是否适配过Android Q（既targetSdkVersion是否大于等于29），均无法再获取到设备IMEI等设备信息。

如果Android10以下设备获取设备IMEI等信息，可以配置最大sdk版本：

```xml
<uses-permission android:name="android.permission.READ_PHONE_STATE"
        android:maxSdkVersion="28"/>
```

# Android 11

- 分区存储强制执行

没错，Android11强制执行分区存储，也就是沙盒模式。这次真的没有关闭功能了，离Android11出来也有一段时间了，还是抓紧适配把。

- 修改电话权限

改动了两个API：getLine1Number()和 getMsisdn() ，需要加上READ_PHONE_NUMBERS权限

- 不允许自定义toast从后台显示了
- 必须加上v2签名
- 增加5g相关API
- 后台位置访问权限再次限制

你一定很奇怪，为什么`Android11`的适配就这么草草收尾了？这可是我们最需要的啊？

哈哈，因为改动还是挺多的，所以给你推荐文章—`Android11最全适配指南`，应该有很多朋友都看过了：https://juejin.cn/post/6860370635664261128，或者点击文末的“阅读原文”即可。

# 参考

https://juejin.cn/post/6898176468661059597 https://blog.csdn.net/qq_17766199/article/details/80965631 https://weilu.blog.csdn.net/article/details/98336225

# 相关阅读

[Android 适配之版本适配](http://mp.weixin.qq.com/s?__biz=MzIxNzU1Nzk3OQ==&mid=2247488324&idx=1&sn=617d63108f012d02d426817ee62978cd&chksm=97f6adf0a08124e652c7c03bfb956c6a3823a1aab4d1ace50042a2db2c76cce2c903379355b4&scene=21#wechat_redirect)

[谷歌：未来 Android 手机将获得 4 年软件更新](http://mp.weixin.qq.com/s?__biz=MzIxNzU1Nzk3OQ==&mid=2247491967&idx=1&sn=631436731bd07a3f6b8dbb90ec513ce0&chksm=97f55fcba082d6dd0e1180ab7d505ce4d5786d8acb4f81d44afc47360739f2e4d7d0705d1e7b&scene=21#wechat_redirect)

[Android-图片的选择，裁剪，压缩，适配高版本](http://mp.weixin.qq.com/s?__biz=MzIxNzU1Nzk3OQ==&mid=2247487853&idx=1&sn=1ab4a680b0d9e76f2226311f1e2ae53f&chksm=97f6afd9a08126cfef73a185fd984bf8cfda36c24573134fde196fb9ade4278f5a61f4a6a14e&scene=21#wechat_redirect)