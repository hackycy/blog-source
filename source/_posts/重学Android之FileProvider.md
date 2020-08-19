---
title: 重学Android之FileProvider
date: 2020-08-19 10:55:20
keywords:
    - Android
tags:
    - Android
categories:
    - Android
---

# 前言

项目之前使用了第三方库的时候，对于`FileProvider`的适配还不是很了解，因为使用时第三方库已经进行了适配。但是自己去覆写别人的第三方库的时候了解到了`FileProvider`的适配。

对于Android 7.0，提供了非常多的变化，详细的可以阅读官方文档[Android 7.0 行为变更](https://developer.android.com/about/versions/nougat/android-7.0-changes.html)，但是该文章主要叙述关于`FileProvider`的适配。

>  在官方7.0的以上的系统中，尝试传递 `file://URI`可能会触发`FileUriExposedException`。

<!-- more -->

# 出错案例

先来一个常用的例子，大家应该对于手机拍照一定都不陌生，在希望得到一张高清拍照图的时候，我们通过Intent会传递一个File的Uri给相机应用。

大致代码如下:

``` java
private static final int REQUEST_CODE_TAKE_PHOTO = 0x110;
    private String mCurrentPhotoPath;

    public void takePhotoNoCompress(View view) {
        Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
        if (takePictureIntent.resolveActivity(getPackageManager()) != null) {

            String filename = new SimpleDateFormat("yyyyMMdd-HHmmss", Locale.CHINA)
                    .format(new Date()) + ".png";
            File file = new File(Environment.getExternalStorageDirectory(), filename);
            mCurrentPhotoPath = file.getAbsolutePath();
						// File -> Uri 
            takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT, Uri.fromFile(file));
            startActivityForResult(takePictureIntent, REQUEST_CODE_TAKE_PHOTO);
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (resultCode == RESULT_OK && requestCode == REQUEST_CODE_TAKE_PHOTO) {
            mIvPhoto.setImageBitmap(BitmapFactory.decodeFile(mCurrentPhotoPath));
        }
        // else tip?

}
```

> 未处理6.0权限，有需要的自行处理下，nexus系列如果未处理，需要手动在设置页开启存储权限。

此时如果我们使用Android 7.0或者以上的原生系统，再次运行一下，你会发现应用直接停止运行，抛出了`android.os.FileUriExposedException`：

``` bash
Caused by: android.os.FileUriExposedException: 
    file:///storage/emulated/0/20170601-030254.png 
        exposed beyond app through ClipData.Item.getUri()
    at android.os.StrictMode.onFileUriExposed(StrictMode.java:1932)
    at android.net.Uri.checkFileUriExposed(Uri.java:2348)
```

原因在官网已经给了解释：

> 对于面向 Android 7.0 的应用，Android 框架执行的 StrictMode API 政策禁止在您的应用外部公开 file:// URI。如果一项包含文件 URI 的 intent 离开您的应用，则应用出现故障，并出现 FileUriExposedException 异常。

同样的，官网也给出了解决方案：

> 要在应用间共享文件，您应发送一项 content:// URI，并授予 URI 临时访问权限。进行此授权的最简单方式是使用 FileProvider 类。如需了解有关权限和共享文件的详细信息，请参阅共享文件。
> https://developer.android.com/about/versions/nougat/android-7.0-changes.html#accessibility

# 使用FileProvider

`FileProvider`属于Android 7.0新增的一个类，该类位于v4或者androidx包下，详情可见`android.support.v4.content.FileProvider`或者`androidx.core.content.FileProvider`，使用方法类似与`ContentProvider`，简单概括为三个步骤，这里先以调用系统相机拍照并保存**sdcard**公共目录为例，演示使用过程：

- 在资源文件夹`res/xml`下新建`file_paths.xml`文件，文件声明权限请求的路径，代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<paths xmlns:android="http://schemas.android.com/apk/res/android">
	<!--3、对应外部内存卡根目录：Environment.getExternalStorageDirectory()-->
	<external-path name="external" path="/" />
</paths>
```

> 要使用`content://uri`替代`file://uri`，需要一个虚拟的路径对文件路径进行映射，所以需要编写个xml文件，通过path以及xml节点确定可访问的目录，通过name属性来映射真实的文件路径。

- 在`AndroidManifest.xml`添加组件`provider`相关信息，类似组件`activity`，指定`resource`属性引用上一步创建的xml文件（后面会详细介绍各个属性的用法），代码如下：

```xml
<!-- 定义FileProvider -->
<provider
	android:name="androidx.core.content.FileProvider"
	android:authorities="com.siyee.android7.fileprovider"
	android:exported="false"
	android:grantUriPermissions="true">
	<meta-data
		android:name="android.support.FILE_PROVIDER_PATHS"
		android:resource="@xml/file_paths"/>
</provider>
```

- 最后一步，Java代码申请权限，使用新增的方法`getUriForFile()`和`grantUriPermission()`，代码如下（后面会详细介绍方法对应参数的使用）：

```java
public void takePhotoNoCompress(View view) {
        Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
        if (takePictureIntent.resolveActivity(getPackageManager()) != null) {

            String filename = new SimpleDateFormat("yyyyMMdd-HHmmss", Locale.CHINA)
                    .format(new Date()) + ".png";
            File file = new File(Environment.getExternalStorageDirectory(), filename);
            mCurrentPhotoPath = file.getAbsolutePath();
          
						// 核心就是这一行代码
            Uri fileUri = FileProvider.getUriForFile(this, "com.siyee.android7.fileprovider", file);
            takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT, fileUri);
            startActivityForResult(takePictureIntent, REQUEST_CODE_TAKE_PHOTO);
        }
    }
```

> 通过FileProvider把`file`转化为`content://uri`了

核心代码就这一行了~

```
FileProvider.getUriForFile(this, "com.siyee.android7.fileprovider", file);
```

第二个参数就是我们配置的`authorities`，这个很正常了，总得映射到确定的ContentProvider吧~所以需要这个参数。

然后再看一眼我们生成的uri：

``` java
content://com.siyee.android7.fileprovider/external/20200819-041411.png
```

可以看到格式为：`content://authorities/定义的name属性/文件的相对路径`，即`name`隐藏了可存储的文件夹路径。

# 兼容

如果使用以上代码跑在7.0以上系统的手机没有问题，但是拿回到低版本的手机又会出现崩溃：

``` log
Caused by: java.lang.SecurityException: Permission Denial: opening provider androidx.core.content.FileProvider from ProcessRecord{52b029b8 1670:com.android.camera/u0a36} (pid=1670, uid=10036) that is not exported from uid 10052
at android.os.Parcel.readException(Parcel.java:1465)
at android.os.Parcel.readException(Parcel.java:1419)
at android.app.ActivityManagerProxy.getContentProvider(ActivityManagerNative.java:2848)
at android.app.ActivityThread.acquireProvider(ActivityThread.java:4399)
```

因为低版本的系统，仅仅是把这个当成一个普通的Provider在使用，而我们没有授权，`contentprovider`的export设置的也是false；导致`Permission Denial`。

而解决的办法就是授权了。通过`grantUriPermission(String toPackage, Uri uri,
int modeFlags)`和`revokeUriPermission(Uri uri, int modeFlags)`方法。

可以看到`grantUriPermission`需要传递一个包名，就是你给哪个应用授权，但是很多时候，比如分享，我们并不知道最终用户会选择哪个app，所以我们可以这样：

``` java
List<ResolveInfo> resInfoList = context.getPackageManager()
            .queryIntentActivities(intent, PackageManager.MATCH_DEFAULT_ONLY);
for (ResolveInfo resolveInfo : resInfoList) {
    String packageName = resolveInfo.activityInfo.packageName;
    context.grantUriPermission(packageName, uri, flag);
}
```

根据Intent查询出的所以符合的应用，都给他们授权~~

恩，你可以在不需要的时候通过`revokeUriPermission`移除权限~

那么增加了授权后的代码是这样的：

``` java
public void takePhotoNoCompress(View view) {
    Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
    if (takePictureIntent.resolveActivity(getPackageManager()) != null) {

        String filename = new SimpleDateFormat("yyyyMMdd-HHmmss", Locale.CHINA)
                .format(new Date()) + ".png";
        File file = new File(Environment.getExternalStorageDirectory(), filename);
        mCurrentPhotoPath = file.getAbsolutePath();

        Uri fileUri = FileProvider.getUriForFile(this, "com.siyee.android7.fileprovider", file);

        List<ResolveInfo> resInfoList = getPackageManager()
                .queryIntentActivities(takePictureIntent, PackageManager.MATCH_DEFAULT_ONLY);
        for (ResolveInfo resolveInfo : resInfoList) {
            String packageName = resolveInfo.activityInfo.packageName;
            grantUriPermission(packageName, fileUri, Intent.FLAG_GRANT_READ_URI_PERMISSION
                    | Intent.FLAG_GRANT_WRITE_URI_PERMISSION);
        }

        takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT, fileUri);
        startActivityForResult(takePictureIntent, REQUEST_CODE_TAKE_PHOTO);
    }
}
```

但是这样的做法相对麻烦，我们可以对系统版本进行判断高版本用`FileProvider.getUriForFile`，低版本继续使用`Uri.fromFile`即可。

``` java
Uri fileUri = null;
if (Build.VERSION.SDK_INT >= 24) {
    fileUri = FileProvider.getUriForFile(this, "com.siyee.android7.fileprovider", file);
} else {
    fileUri = Uri.fromFile(file);
}
```

# 理解FileProvider

## 定义FileProvider

直接使用`FileProvider`本身或者它的子类，需要在`AndroidManifest.xml`文件中声明组件的相关属性，包括：

- `android:name`，对应属性值：`android.support.v4.content.FileProvider`或者子类完整路径
- `android:authorities`，对应属性值是一个常量，通常定义的方式`packagename.fileprovider`，例如：`cn.teachcourse.fileprovider`
- `android:exported`，对应属性值是一个boolean变量，设置为`false`
- `android:grantUriPermissions`，对应属性值也是一个boolean变量，设置为`true`，允许获得文件临时的访问权限

``` xml
<manifest>
    ...
    <application>
        ...
        <provider
					android:name="androidx.core.content.FileProvider"
					android:authorities="com.siyee.android7.fileprovider"
					android:exported="false"
					android:grantUriPermissions="true">
					<meta-data
						android:name="android.support.FILE_PROVIDER_PATHS"
						android:resource="@xml/file_paths"/>
					</provider>
        ...
    </application>
</manifest>
```

## 指定授予临时访问权限的文件目录

想要关联`res/xml`文件夹下创建的`file_paths.xml`文件，需要在`<provider>`标签内，添加`<meta-data>`子标签，设置`<meta-data>`标签的属性值，包括：

- `android:name`，对应属性值是一个固定的系统常量`android.support.FILE_PROVIDER_PATHS`
- `android:resource`，对应属性值指向我们的xml文件`@xml/file_paths`

在xml文件中指定文件存储的区块和区块的相对路径，在`<paths>`根标签中添加`<files-path>`子标签（稍后详细列出所有子标签），设置子标签的属性值，包括：

- `name`，是一个虚设的文件名（可以自由命名），对外可见路径的一部分，隐藏真实文件目录
- `path`，是一个相对目录，相对于当前的子标签`<files-path>`根目录
- `<files-path>`，表示内部内存卡根目录，对应根目录等价于`Context.getFilesDir()`，查看完整路径：
  `/data/user/0/com.siyee.demos/files`
- 代码如下：

```xml
<paths xmlns:android="http://schemas.android.com/apk/res/android">
    <files-path name="my_images" path="images/"/>
    ...
</paths>
```

`<paths>`根标签下可以添加的子标签也是有限的，参考官网的开发文档，除了上述的提到的`<files-path>`这个子标签外，还包括下面几个：

1. `<cache-path>`，表示应用默认缓存根目录，对应根目录等价于`getCacheDir()`，查看完整路径：`/data/user/0/com.siyee.demos/cache`
2. `<external-path>`，表示外部内存卡根目录，对应根目录等价于
   `Environment.getExternalStorageDirectory()`，
   查看完整路径：`/storage/emulated/0`
3. `<external-files-path>`，表示外部内存卡根目录下的APP公共目录，对应根目录等价于
   `Context#getExternalFilesDir(String) Context.getExternalFilesDir(null)`，
   查看完整路径：
   `/storage/emulated/0/Android/data/com.siyee.demos/files/Download`
4. `<external-cache-path>`，表示外部内存卡根目录下的APP缓存目录，对应根目录等价于`Context.getExternalCacheDir()`，查看完整路径：
   `/storage/emulated/0/Android/data/com.siyee.demos/cache`

最终，在`file_provider.xml`文件中，添加上述5种类型的临时访问权限的文件目录，代码如下：

``` xml
<?xml version="1.0" encoding="utf-8"?>
<paths xmlns:android="http://schemas.android.com/apk/res/android">
    <!--
    1、name对应的属性值，开发者可以自由定义；
    2、path对应的属性值，当前external-path标签下的相对路径
    比如：/storage/emulated/0/92Recycle-release.apk
    sdcard路径：/storage/emulated/0(WriteToReadActivity.java:176)
                      at cn.teachcourse.nougat.WriteToReadActivity.onClick(WriteToReadActivity.java:97)
                      at android.view.View.performClick(View.java:5610)
                      at android.view.View$PerformClick.run(View.java:22265)
    相对路径：/
    -->
    <!--1、对应内部内存卡根目录：Context.getFileDir()-->
    <files-path
        name="int_root"
        path="/" />
    <!--2、对应应用默认缓存根目录：Context.getCacheDir()-->
    <cache-path
        name="app_cache"
        path="/" />
    <!--3、对应外部内存卡根目录：Environment.getExternalStorageDirectory()-->
    <external-path
        name="ext_root"
        path="pictures/" />
    <!--4、对应外部内存卡根目录下的APP公共目录：Context.getExternalFileDir(String)-->
    <external-files-path
        name="ext_pub"
        path="/" />
    <!--5、对应外部内存卡根目录下的APP缓存目录：Context.getExternalCacheDir()-->
    <external-cache-path
        name="ext_cache"
        path="/" />
</paths>
```

## 生成指定文件的Content URI

Content URI方便与另一个APP应用程序共享同一个文件，共享的方式通过`ContentResolver.openFileDescriptor`获得一个`ParcelFileDescriptor`对象，读取文件内容。那么，如何生成一条完整的Content URI呢？TeachCourse总结后，概括为三个步骤，**第一步：**明确上述5种类型中的哪一种，**第二步：**明确指定文件的完整路径（包括目录、文件名），**第三步：**调用`getUriForFile()`方法生成URI

```java
File imagePath = new File(Environment.getExternalStorageDirectory(), "download");
File newFile = new File(imagePath, "default_image.jpg");
Uri contentUri = getUriForFile(getContext(), "com.siyee.android7.fileprovider", newFile);
```

## 授予Content URI临时访问权限

上一步获得的Content URI，并没有获得指定文件的读写权限，想要获得文件的读写权限需要调用`Context.grantUriPermission(package, Uri, mode_flags)`方法，该方法向指定包名的应用程序申请获得读取或者写入文件的权限，参数说明如下：

- `package`，指定应用程序的包名，Android Studio真正的包名指`build.gradle`声明的*applicationId*属性值；`getPackageName()`指`AndroidManifest.xml`文件声明的*package*属性值，如果两者不一致，就不能提供`getPackageName()`获取包名，否则报错！
- `Uri`，指定请求授予临时权限的URI，例如：`contentUri`
- `mode_flags`，指定授予临时权限的类型，选择其中一个常量或两个：`Intent.FLAG_GRANT_READ_URI_PERMISSION`，`Intent.FLAG_GRANT_WRITE_URI_PERMISSION`

授予文件的临时读取或写入权限，如果不再需要了，TeachCourse该如何撤销授予呢？撤销权限有两种方式：**第一种：**通过调用`revokeUriPermission()`撤销，**第二种：**重启系统后自动撤销

# [使用FileProvider兼容安装apk](https://blog.csdn.net/lmj623565791/article/details/72859156)

正常我们在编写安装apk的时候，是这样的：

```java
public void installApk(View view) {
    File file = new File(Environment.getExternalStorageDirectory(), "testandroid7-debug.apk");

    Intent intent = new Intent(Intent.ACTION_VIEW);
    intent.setDataAndType(Uri.fromFile(file),
            "application/vnd.android.package-archive");
    startActivity(intent);
}
```

拿个7.0的原生手机跑一下，`android.os.FileUriExposedException`又来了~~

```
android.os.FileUriExposedException: file:///storage/emulated/0/testandroid7-debug.apk exposed beyond app through Intent.getData()
```

好在有经验了，简单修改下uri的获取方式。

```java
if (Build.VERSION.SDK_INT >= 24) {
    fileUri = FileProvider.getUriForFile(this, "com.zhy.android7.fileprovider", file);
} else {
    fileUri = Uri.fromFile(file);
}
```

再跑一次，没想到还是抛出了异常（警告，没有Crash）:

```
java.lang.SecurityException: Permission Denial: 
opening provider android.support.v4.content.FileProvider 
        from ProcessRecord{18570a 27107:com.google.android.packageinstaller/u0a26} (pid=27107, uid=10026) that is not exported from UID 10004123
```

可以看到是权限问题，对于权限我们刚说了一种方式为`grantUriPermission`，这种方式当然是没问题的啦~

加上后运行即可。

其实对于权限，还提供了一种方式，即：

```
intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION | Intent.FLAG_GRANT_WRITE_URI_PERMISSION);
```

我们可以在安装包之前加上上述代码，再次运行正常啦~

现在我有两个非常疑惑的问题：

- 问题1：为什么刚才拍照的时候，Android 7的设备并没有遇到`Permission Denial`的问题？

恩，之所以不需要权限，主要是因为Intent的action为`ACTION_IMAGE_CAPTURE`，当我们`startActivity`后，会辗转调用`Instrumentation的execStartActivity`方法，在该方法内部，会调用`intent.migrateExtraStreamToClipData();`方法。

该方法中包含：

```java
if (MediaStore.ACTION_IMAGE_CAPTURE.equals(action)
        || MediaStore.ACTION_IMAGE_CAPTURE_SECURE.equals(action)
        || MediaStore.ACTION_VIDEO_CAPTURE.equals(action)) {
    final Uri output;
    try {
        output = getParcelableExtra(MediaStore.EXTRA_OUTPUT);
    } catch (ClassCastException e) {
        return false;
    }
    if (output != null) {
        setClipData(ClipData.newRawUri("", output));
        addFlags(FLAG_GRANT_WRITE_URI_PERMISSION|FLAG_GRANT_READ_URI_PERMISSION);
        return true;
    }
}
```

可以看到将我们的`EXTRA_OUTPUT`，转为了`setClipData`，并直接给我们添加了`WRITE`和`READ`权限。

> 注：该部分逻辑应该是21之后添加的。

- 问题2：为什么刚才拍照案例的时候，Android 4.4设备遇到权限问题，不通过addFlags这种方式解决？

因为addFlags主要用于`setData`，`setDataAndType`以及`setClipData`（注意：4.4时，并没有将`ACTION_IMAGE_CAPTURE`转为`setClipData`实现）这种方式。

所以`addFlags`方式对于`ACTION_IMAGE_CAPTURE`在5.0以下是无效的，所以需要使用`grantUriPermission`，如果是正常的通过setData分享的uri，使用`addFlags`是没有问题的（可以写个简单的例子测试下，两个app交互，通过`content://`）。

# 总结

使用`content://`替代`file://`，主要需要`FileProvider`的支持，而因为`FileProvider`是`ContentProvider`的子类，所以需要在`AndroidManifest.xml`中注册；而又因为需要对真实的`filepath`进行映射，所以需要编写一个`xml`文档，用于描述可使用的文件夹目录，以及通过`name`去映射该文件夹目录。

对于权限，有两种方式：

- 方式一为`Intent.addFlags`，该方式主要用于针对`intent.setData`，`setDataAndType`以及`setClipData`相关方式传递`uri`的。
- 方式二为`grantUriPermission`来进行授权

# 参考资料

https://blog.csdn.net/lmj623565791/article/details/72859156

https://www.cnblogs.com/dazhao/p/6547811.html

https://www.jianshu.com/p/bce6a4c779dd