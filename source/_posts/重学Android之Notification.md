---
title: 重学Android之Notification
date: 2020-02-11 15:48:37
keywords:
    - Android
tags:
    - Notification
categories:
    - Android
---

# 什么是通知

通知是一个可以在应用程序正常的用户界面之外显示给用户的消息。
通知发出时，它首先出现在状态栏的通知区域中，用户打开通知抽屉可查看通知详情。通知区域和通知抽屉都是用户可以随时查看的系统控制区域。

<!-- more -->

各种通知的展现形式如图：

![](notificationshow.png)

# 通知的基础用法

## 通知创建方式区别

随着Android系统不断升级，Notification的创建方式也随之变化，主要变化如下:

**Android 3.0之前**

Android 3.0 (API level 11)之前，使用`new Notification()`方式创建通知:

``` java
NotificationManager mNotifyMgr = 
      (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
PendingIntent contentIntent = PendingIntent.getActivity(
      this, 0, new Intent(this, ResultActivity.class), 0);

Notification notification = new Notification(icon, tickerText, when);
notification.setLatestEventInfo(this, title, content, contentIntent);

mNotifyMgr.notify(NOTIFICATIONS_ID, notification);
```

**Android 3.0 (API level 11)及更高版本**

Android 3.0开始弃用`new Notification()`方式，改用`Notification.Builder()`来创建通知:

```java
NotificationManager mNotifyMgr = 
      (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
PendingIntent contentIntent = PendingIntent.getActivity(
      this, 0, new Intent(this, ResultActivity.class), 0);

Notification notification = new Notification.Builder(this)
            .setSmallIcon(R.drawable.notification_icon)
            .setContentTitle("My notification")
            .setContentText("Hello World!")
            .setContentIntent(contentIntent)
            .build();// getNotification()

mNotifyMgr.notify(NOTIFICATIONS_ID, notification);
```

> 这里需要注意: `build()` 是Androdi 4.1(API level 16)加入的，用以替代`getNotification()`。API level 16开始弃用`getNotification()`

**兼容Android 3.0之前的版本**

为了兼容`API level 11`之前的版本，`v4 Support Library`中提供了
 `NotificationCompat.Builder()`这个替代方法。它与`Notification.Builder()`类似，二者没有太大区别。

```kotlin
NotificationManager mNotifyMgr = 
      (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
PendingIntent contentIntent = PendingIntent.getActivity(
      this, 0, new Intent(this, ResultActivity.class), 0);

NotificationCompat.Builder mBuilder = 
      new NotificationCompat.Builder(this)
            .setSmallIcon(R.drawable.notification_icon)
            .setContentTitle("My notification")
            .setContentText("Hello World!")
            .setContentIntent(contentIntent);

mNotifyMgr.notify(NOTIFICATIONS_ID, mBuilder.build());
```

> **注：除特别说明外，本文将根据 `NotificationCompat.Builder()` 展开解析，
> `Notification.Builder()`类似。**

## 基本用法

### 通知的必要属性

一个通知必须包含以下三项属性:

- 小图标，对应 setSmallIcon()
- 通知标题，对应 setContentTitle()
- 详细信息，对应 setContentText()

其他属性均为可选项，更多属性方法请参考[NotificationCompat.Builder](https://link.jianshu.com?t=http://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)。

尽管其他都是可选的，但一般都会为通知添加至少一个动作(Action)，这个动作可以是跳转到Activity、启动一个Service或发送一个Broadcas等。 通过以下方式为通知添加动作:

- 使用PendingIntent
- 通过大视图通知的 `Action Button`，仅支持Android 4.1 (API level 16)及更高版本

### 创建一个通知

1、实例化一个NotificationCompat.Builder对象

``` java
NotificationCompat.Builder builderNormal = new NotificationCompat.Builder(this)
                        .setSmallIcon(R.mipmap.ic_launcher)
                        .setContentTitle("普通的通知标题")
                        .setContentText("普通的通知内容");
```

NotificationCompat.Builder自动设置的默认值:

- **priority:** PRIORITY_DEFAULT
- **when: **System.currentTimeMillis()
- **audio stream: **STREAM_DEFAULT

2、定义并设置一个通知动作(Action)

```java
Intent secIntent = new Intent(this, SecActivity.class);
PendingIntent secPendingIntent = PendingIntent.getActivity(this, 0,
        secIntent, PendingIntent.FLAG_UPDATE_CURRENT);
builder.setContentIntent(secPendingIntent);
```

PendingIntent 是一种特殊的 Intent ，字面意思可以解释为延迟的 Intent ，用于在某个事件结束后执行特定的 Action 。从上面带 Action 的通知也能验证这一点，当用户点击通知时，才会执行。
PendingIntent 是 Android 系统管理并持有的用于描述和获取原始数据的对象的标志(引用)。也就是说，**即便创建该PendingIntent对象的进程被杀死了，这个PendingItent对象在其他进程中还是可用的**。
日常使用中的短信、闹钟等都用到了 PendingIntent。

PendingIntent 主要可以通过以下三种方式获取：

``` java
//获取一个用于启动 Activity 的 PendingIntent 对象
public static PendingIntent getActivity(Context context, int requestCode, Intent intent, int flags);

//获取一个用于启动 Service 的 PendingIntent 对象
public static PendingIntent getService(Context context, int requestCode, Intent intent, int flags);

//获取一个用于向 BroadcastReceiver 广播的 PendingIntent 对象
public static PendingIntent getBroadcast(Context context, int requestCode, Intent intent, int flags)
```

PendingIntent 具有以下几种 flag：

- **FLAG_CANCEL_CURRENT:**如果当前系统中已经存在一个相同的 PendingIntent 对象，那么就将先将已有的 PendingIntent 取消，然后重新生成一个 PendingIntent 对象。
- **FLAG_NO_CREATE:**如果当前系统中不存在相同的 PendingIntent 对象，系统将不会创建该 PendingIntent 对象而是直接返回 null 。
- **FLAG_ONE_SHOT:**该 PendingIntent 只作用一次。
- **FLAG_UPDATE_CURRENT:**如果系统中已存在该 PendingIntent 对象，那么系统将保留该 PendingIntent 对象，但是会使用新的 Intent 来更新之前 PendingIntent 中的 Intent 对象数据，例如更新 Intent 中的 Extras。

3、生成`Notification`对象

```java
Notification notificationNormal = builderNormal.build();
```

4、使用`NotificationManager`发送通知

```java
NotificationManager mNotificationManager;
mNotificationManager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
// 第一个参数为ID，后续更新通知会使用到
mNotificationManager.notify(0, notificationNormal);
```

### 更新通知

更新通知很简单，只需再次发送相同ID的通知即可，如果之前的通知依然存在则会更新通知属性，如果之前通知不存在则重新创建。

```java
NotificationCompat.Builder builder = new NotificationCompat.Builder(this)
        .setSmallIcon(R.mipmap.ic_launcher)
        .setContentTitle("更新的通知标题")
        .setContentText("更新的通知内容");

builder.setContentIntent(secPendingIntent);

Notification notificationUpdate = builder.build();
// 保证与上述创建的相同ID即可。
mNotificationManager.notify(0, notificationUpdate);
```

### 取消通知

取消通知有如下几种方式:

- 点击通知栏的清除按钮，会清除所有可清除的通知
- 设置了 setAutoCancel() 或 FLAG_AUTO_CANCEL 的通知，点击该通知时会清除它
- 通过 NotificationManager 调用 cancel(int id) 方法清除指定 ID 的通知
- 通过 NotificationManager 调用 cancel(String tag, int id) 方法清除指定 TAG 和 ID 的通知
- 通过 NotificationManager 调用 cancelAll() 方法清除所有该应用之前发送的通知

添加`setAutoCancel`方法

```java
NotificationCompat.Builder builderNormal = new NotificationCompat.Builder(this)
        .setAutoCancel(true)
        .setSmallIcon(R.mipmap.ic_launcher)
        .setContentTitle("普通的通知标题")
        .setContentText("普通的通知内容");
```

## 通知类型

### 大视图通知

通知有两种视图：普通视图和大视图。

普通视图：

![](normalview.png)

大视图（BigText为例）：

![](bigtextview.png)

默认情况下为普通视图，可通过`NotificationCompat.Builder.setStyle()`设置大视图。

> 注: 大视图(Big Views)由Android 4.1(API level 16)开始引入，且仅支持Android 4.1及更高版本。

以上图为例:
1、构建Action Button的PendingIntent

``` java
Intent dismissIntent = new Intent(this, PingService.class);
                dismissIntent.setAction("dismiss");
                PendingIntent p1 = PendingIntent.getService(this, 0, dismissIntent, PendingIntent.FLAG_UPDATE_CURRENT);

                Intent snoozeIntent = new Intent(this, PingService.class);
                snoozeIntent.setAction("snooze");
                PendingIntent p2 = PendingIntent.getService(this, 0, snoozeIntent, PendingIntent.FLAG_UPDATE_CURRENT);
```

2、构建NotificatonCompat.Builder对象

``` java
NotificationCompat.Builder builderBig = new NotificationCompat.Builder(this)
                        .setSmallIcon(R.mipmap.ic_launcher)
                        .setContentTitle(getString(R.string.title))
                        .setContentText("简要")
                        .setDefaults(Notification.DEFAULT_ALL)
  											// 注意该步骤
                        .setStyle(new NotificationCompat.BigTextStyle().bigText(getString(R.string.content)))
                        .addAction(android.R.drawable.ic_input_delete, "dimiss", p1)
                        .addAction(android.R.drawable.ic_input_add, "snooze", p2);
```

3、其他步骤与普通视图相同

### 进度条通知

**明确进度的进度条**

使用`setProgress(max, progress, false)`来更新进度。

- max: 最大进度值
- progress: 当前进度
- false: 是否是不明确的进度条



## 通知常见属性



# 参考资料

https://developer.android.com/guide/topics/ui/notifiers/notifications.html

https://developer.android.com/reference/android/app/Notification.html

https://www.cnblogs.com/travellife/p/Android-Notification-xiang-jie-yiji-ben-cao-zuo.html