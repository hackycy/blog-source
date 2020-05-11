---
title: Android进程保活探讨
date: 2020-02-04 14:04:19
keywords:
    - Android
tags:
    - 进程保活
categories:
    - Android
---

# 前言

Android 系统为了保持系统运行流畅，在内存吃紧的情况下，会将一些进程 kill ，以释放一部分内存。然而，对于一些（如：IM-QQ 、微信，支付-支付宝等）比较重要、我们希望能及时收到消息的 APP，需要保持进程持续活跃，那么就需要实施一些保活措施来保证进程能够持续存活，即 **Android 进程保活**。

<!-- more -->

**Android 进程拉活包括两个层面：**

- 提供进程优先级，降低进程被杀死的概率

- 在进程被杀死后，进行拉活

在此之前，先来了解下 Android 进程的一些相关概念。

# 进程

默认情况下，同一 APP 的所有组件均运行在相同的进程中，但是也可以根据需要，通过在清单文件中配置来控制某些组件的所属进程。

内存不足的情况下，Android 系统会选择 kill 某一进程来释放该进程占用的内存，供其它为用户提供更为紧急服务的进程使用。在被关闭的进程中运行的组件也会随着进程的关闭而销毁。

决定 kill 哪个进程时，Android 系统将权衡所有进程对用户的相对重要程度。例如：相对于托管可见 Activity 的进程而言，更有可能 kill 托管不可见 Activity 的进程。因此，是否终止 kill 某个进程取决于该进程中所运行组件的状态。

# 进程的优先级

Android 系统将尽量长时间地保持应用进程，但为了新建进程或运行更重要的进程，最终需要清除旧进程来回收内存。 为了确定保留或终止哪些进程，系统会根据进程中正在运行的组件以及这些组件的状态，将每个进程放入“重要性层次结构”中。 必要时，系统会首先消除重要性最低的进程，然后是清除重要性稍低一级的进程，依此类推，以回收系统资源。

进程的重要性，划分5级：

1. 前台进程 (Foreground process)
2. 可见进程 (Visible process)
3. 服务进程 (Service process)
4. 后台进程 (Background process)
5. 空进程 (Empty process)

![](jinchengyouxianji.jpeg)

前台进程的重要性最高，依次递减，空进程的重要性最低，下面分别来阐述每种级别的进程

## 前台进程

用户当前操作的进程。一个进程满足以下任一条件 ，即视为前台进程：

- 托管用户正在交互的 Activity（已调用 onResume() 方法）。
- 托管某个 Service ，且 Service 绑定到用户正在交互的 Activity。
- 托管正在“前台”运行的 Service（服务已调用startForeground()）。
- 托管正在执行生命周期回调的 Service（ onCreate() 、 onStart() 或 onDestory() ）。
- 托管正在执行 onReceive() 方法的 BroadcastReceiver。

> 通常，任意时间的前台进程数据都不多。只有在内存不足以支持它们同时继续运行这一万不得已的情况下，系统才会 kill 它们。

## 可见进程

没有任何前台组件、但仍会影响用户在屏幕上所见内容的进程。 如果一个进程满足以下任一条件，即视为可见进程：

- 托管不在前台、但仍对用户可见的 Activity（已调用 onPause() 方法）。如：前台 Activity 启动了一个对话框，允许在其后面显示上一个 Activity。
- 托管绑定到可见（或前台）的 Activity 的 Service。

> 可见进程被视为及其重要的进程，除非为了维持所有前台进程同时运行而必须终止，否则系统不会kill这些进程。

## 服务进程

正在运行已使用 startService() 方法启动的 Service 且不属于上述两个更高类别进程的进程。

> 尽管服务进程与用户可见内容没有直接关联，但是它们通常在执行一些用户比较关心的操作（如：在后台播放音乐或从网络下载数据等），因此，除非内部不足以维持所有前台进程和可见进程同时运行，否则系统不会 kill 这些进程。

## 后台进程

托管目前对用户不可见的 Activity 的进程（已调用 Activity 的 onStop() 方法）。

> 后台进程对用户体验没有直接影响，系统可能随时会 kill 它们，以回收内存提供给前台进程、可见进程、服务进程使用。通常会有很多后台进程同时运行，系统将它们保存在 LRU（最近最少使用）列表中，以确保包含用户最近查看的 Activity 的进程最后一个被终止。

## 空进程

不包含任何活动组件的进程。

> 保留这种进程的唯一目的是缓存，以缩短下次在其中运行的组件的启动时间。为使系统总体资源在进程缓存和底层内核缓存之间保持平衡，系统往往会kill这些进程。

根据进程中当前活动的组件的重要程度，Android 系统会将进程评定为可能达到的最高级别。比如，托管服务和可见 Activity 的进程，系统会将其评定为可见进程，而不是服务进程。

此外，一个进程的级别可能会因为其他进程对其依赖而有所提高，即服务于另一进程的进程其级别不会低于其服务的进程。例如，进程 A 中的 Service 绑定到进程 B 中的组件，则进程 A 始终被视为至少和进程 B 同等级别。

由于运行 Service 的进程其级别高于托管后台 Activity 的进程，因此在要做长时间后台操作的 Activity 中最好为该操作启动 Service，而不是简单的创建子线程，当操作有可能比 Activity 更持久时更需如此。例如，需要上传较大图片或较大文件的 Activity，应该启动 Service 来执行上传操作，这样，即使 Activity 被销毁，Service 仍能在后台继续执行上传操作。使用 Service 执行较长耗时操作，可以保证不管 Activity 发生什么情况，该操作至少有服务进程的优先级。同理，使用广播接收器时，也当如此。

> https://developer.android.com/guide/components/processes-and-threads.html

# 进程回收策略

Android 系统回收进程内存的机制叫`Low Memory Killer`机制，是一种根据 `oom_adj`阈值级别触发相应力度的内存回收的机制。`oom_adj` 代表进程的优先级，数值越高，优先级越低，越容易被杀死。

**Andorid的 Low Memory Killer 是在标准的linux lernel的 OOM 基础上修改而来的一种内存管理机制。当系统内存不足时，杀死不必要的进程释放其内存。不必要的进程的选择根据有2个：oom_adj和占用的内存的大小。oom_adj 代表进程的优先级，数值越高，优先级越低，越容易被杀死；对应每个oom_adj都可以有一个空闲进程的阀值。**

**Android Kernel每隔一段时间会检测当前空闲内存是否低于某个阀值。假如是，则杀死oom_adj最大的不必要的进程，如果有多个，就根据 oom_score_adj 去杀死进程，直到内存恢复低于阀值的状态。**

关于 oom_adj 的说明如下：

![](oomadj.jpeg)

> 其中红色部分代表比较容易被杀死的 Android 进程（OOM_ADJ>=4）,绿色部分表示不容易被杀死的 Android 进程，其他表示非 Android 进程（纯 Linux 进程）。
>
> 在 Lowmemorykiller 回收内存时会根据进程的级别优先杀死 OOM_ADJ 比较大的进程，对于优先级相同的进程则进一步受到进程所占内存和进程存活时间的影响。
>
> **普通app进程的oom_adj>=0,系统进程的oom_adj才可能<0**
>
> 查看当前应用进程adj值命令为：`cat /proc/进程id/oom_adj`
>
> 查看当前进程id：`ps | grep PackageName`

**LowMemoryKiller 的阈值的设定，主要保存在2个文件之中，分别是:**

- `/sys/module/lowmemorykiller/parameters/adj`
- `/sys/module/lowmemorykiller/parameters/minfree`

adj保存着当前系统杀进程的等级，minfree则是保存着对应的内存阀值。

在API为26的Nexus模拟器下打印输出的值为：

``` bash
generic_x86:/ # cat /sys/module/lowmemorykiller/parameters/minfree                                                                                                                                        
18432,23040,27648,32256,36864,46080
generic_x86:/ # cat /sys/module/lowmemorykiller/parameters/adj                                                                                                                                            
0,100,200,300,900,906
```

内存阀值在不同手机上不一样，一旦低于该值，Android便会杀死对应优先级的进程。**例如上述手机中，当可用内存小于72MB（18432）时，就杀死前台进程；当可用内存小于180MB（46080）时，则杀死空进程。即跟照上述打印顺序依次杀死为`前台进程 -> 可见进程 -> 服务进程 -> 后台进程 -> Content Provider -> 空进程`。**

> 阀值单位为page，即4kb。

**Android 手机中进程被杀死可能有如下情况：**

![](killevent.jpg)

综上，可以得出减少进程被杀死概率无非就是想办法提高进程优先级，减少进程在内存不足等情况下被杀死的概率。

# 提升进程优先级的方案

## 利用 Activity 提升权限

通过监控手机锁屏，在屏幕锁屏时启动1个像素的Activity，在用户解锁时将Activity销毁掉。

进程的优先级在屏幕锁屏时间由4提升为最高优先级0。

主要解决第三方应用及系统管理工具在检测到锁屏事件后一段时间（一般为5分钟以内）内会杀死后台进程，已达到省电的目的问题。**但是在Android P之后后台都作了限制后该方案无效。测试7.0前的版本稳定。但是不建议使用。没有销毁掉1像素Activity时候会产生严重的体验问题。**

### 具体方案实现

自定义Activity，并且设置Activity大小为1像素。

```java
public class SinglePixelActivity extends Activity {

    private static final String TAG = SinglePixelActivity.class.getSimpleName();

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Window window = getWindow();
        window.setGravity(Gravity.LEFT | Gravity.TOP);
        WindowManager.LayoutParams layoutParams = window.getAttributes();
        layoutParams.x = 0;
        layoutParams.y = 0;
        layoutParams.width = 1;
        layoutParams.height = 1;
        window.setAttributes(layoutParams);
        //setting
        KeepManager.getInstance().setSinglePixelActivity(this);
    }

    @Override
    protected void onDestroy() {
        // 作一些唤醒服务动作
        super.onDestroy();
    }
}
```

其次，从 AndroidManifest 中通过如下属性，排除 Activity 在 RecentTask 中的显示：

``` xml
<activity 
           android:name=".onepixel.SinglePixelActivity" 
           android:configChanges="keyboardHidden|orientation|screenSize|navigation|keyboard"
					 android:excludeFromRecents="true"
           android:exported="false"
           android:finishOnTaskLaunch="false"
           android:launchMode="singleInstance"
           android:theme="@style/SinglePixelActivityStyle">
</activity>
```

> **讲解一下：**
>
> - **android:launchMode属性：**用于指定activity的启动模式，总共分为四种，即：
>   + **standar模式**，每次启动activity都会创建其实例，并加入到任务栈的栈顶；
>   + **singleTop模式**，每次启动activity如果栈顶时该activity则无需创建，其余情况都要创建该activity的实例；
>   + **singleTask模式**，如果被启动的activity的实例存在栈中，则不需要创建，只需要把此activity加入到栈顶，并把该activity以上的activity实例全部pop；
>   + **singleInstance模式**：将创建的activity实例放入单独的栈中，该栈只能存储这个实例，且是作为共享实例存在。
> - **android:configChanges属性：**用于捕获手机状态的改变，即当手机状态(如切换横竖屏、屏幕大小)改变时会保存当前活动状态重启Activity，由于SinglePixelActivity肩负着保活的特殊使命，这里使用**android:configChanges**属性：防止Activity重启，它只是调用了**onConfigurationChanged(Configuration newConfig)**来通知手机状态的改变；
> - **android:excludeFromRecents属性：**用于控制SinglePixelActivity不在最近任务列表中显示；
> - **android:finishOnTaskLaunch属性：**用于标记当用户再起启动应用(TASK)时是否关闭已经存在的Activity的实例，false表示不关闭；
> - **android:theme属性：**用于指定Activity显示主题，这里我们自定义主题SingleActivityStyle。

设置Activity样式。

```xml
<!-- 对1像素Activity进行特殊处理 -->
<style name="SinglePixelActivityStyle">
    <item name="android:windowBackground">@android:color/transparent</item>
    <item name="android:windowIsTranslucent">true</item>
    <item name="android:windowFrame">@null</item>
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowIsFloating">true</item>
    <item name="android:windowContentOverlay">@null</item>
    <item name="android:backgroundDimEnabled">false</item>
    <item name="android:windowAnimationStyle">@null</item>
    <item name="android:windowDisablePreview">true</item>
    <item name="android:windowNoDisplay">false</item>
</style>
```

自定义广播接收者。

```java
public class ScreenBroadcastReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        String action = intent.getAction();
      	// Activity 启动与销毁时机的控制：
        if (Intent.ACTION_SCREEN_OFF.equals(action)) {
            KeepManager.getInstance().startSinglePixelActivity(context);
        } else if (Intent.ACTION_SCREEN_ON.equals(action) || Intent.ACTION_USER_PRESENT.equals(action)) {
            KeepManager.getInstance().finishSinglePixelActivity();
        }
    }

}
```

管理类：

```java
public class ScreenManager {

    private static final String TAG = ScreenManager.class.getSimpleName();

    private final static ScreenManager mInstance = new ScreenManager();

    private ScreenBroadcastReceiver mKeepBroadcastReceiver;

    private WeakReference<Activity> mActivity;

    public static ScreenManager getInstance() {
        return mInstance;
    }

    public void registerKeepReceiver(Context context) {
        mKeepBroadcastReceiver = new ScreenBroadcastReceiver();
        IntentFilter filter = new IntentFilter();
        filter.addAction(Intent.ACTION_SCREEN_OFF);
        filter.addAction(Intent.ACTION_SCREEN_ON);
        context.registerReceiver(mKeepBroadcastReceiver, filter);
    }

    public void unregisterKeepReceiver(Context context) {
        if (mKeepBroadcastReceiver != null) {
            context.unregisterReceiver(mKeepBroadcastReceiver);
        }
    }

    /**
     * 持有SinglePixelActivity的弱引用
     * @param activity
     */
    public void setSinglePixelActivity(Activity activity) {
        mActivity = new WeakReference<>(activity);
    }

    /**
     * 启动SinglePixelActivity
     */
    public void startSinglePixelActivity(Context context) {
        Intent intent = new Intent(context, SinglePixelActivity.class);
        intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        context.startActivity(intent);
    }

    /**
     * 结束SinglePixelActivity
     */
    public void finishSinglePixelActivity() {
        if (mActivity != null) {
            Activity activity = mActivity.get();
            if (activity != null) {
                activity.finish();
            }
        }
    }

}
```

在需要使用的地方使用，如MainActivity：

``` java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        // 1像素保活
        activeSinglePixelActivity();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        ScreenManager.getInstance().unregisterKeepReceiver(this);
    }

    public void activeSinglePixelActivity() {
        ScreenManager.getInstance().registerKeepReceiver(this);
    }

}
```

## 利用前台 Service 提升权限

Android 中 Service 的优先级为4，通过 setForeground 接口可以将后台 Service 设置为前台 Service，使进程的优先级由4提升为2，从而使进程的优先级仅仅低于用户当前正在交互的进程，与可见进程优先级一致，使进程被杀死的概率大大降低。

从 Android2.3 开始调用 setForeground 将后台 Service 设置为前台 Service 时，必须在系统的通知栏发送一条通知，也就是前台 Service 与一条可见的通知时绑定在一起的。

对于不需要常驻通知栏的应用来说，该方案虽好，但却是用户感知的，无法直接使用。

对于 API level < 18 ：调用startForeground(ID， new Notification())，发送空的Notification ，图标则不会显示。对于 API level >= 18：在需要提优先级的service A启动一个InnerService，两个服务同时startForeground，且绑定同样的 ID。Stop 掉InnerService ，这样通知栏图标即被移除。这方案实际利用了Android前台service的漏洞。

**该方案适用范围：7.1.1系统以下**，8.0后的系统通知栏API的变更以及前台服务的变更导致通知栏常驻，造成用户感知。

### 具体方案实现

```java
public class ProcessService extends Service {

    private final static String TAG = ProcessService.class.getSimpleName();

    private final static int PROCESS_SERVICE_ID = -10001;

    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
        throw new UnsupportedOperationException("Not yet implemented");
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        if (Build.VERSION.SDK_INT < 18) {
            // API < 18 ，此方法能有效隐藏Notification上的图标
            startForeground(PROCESS_SERVICE_ID, new Notification());
        } else {
            Intent innerIntent = new Intent(this, ProcessInnerService.class);
            startService(innerIntent);
            startForeground(PROCESS_SERVICE_ID, new Notification());
        }
        return START_STICKY;
    }

    /**
     * 给 API >= 18 的平台上用的保活手段
     */
    public static class ProcessInnerService extends Service {

        @Nullable
        @Override
        public IBinder onBind(Intent intent) {
            throw new UnsupportedOperationException("Not yet implemented");
        }

        @Override
        public int onStartCommand(Intent intent, int flags, int startId) {
            startForeground(PROCESS_SERVICE_ID, new Notification());
            stopSelf();
            return super.onStartCommand(intent, flags, startId);
        }
    }

}
```

# 进程死后拉活的方案

## 利用系统广播拉活

在发生特定系统事件时，系统会发出响应的广播，通过在 AndroidManifest 中“静态”注册对应的广播监听器，即可在发生响应事件时拉活。

常用的用于拉活的广播事件包括：

![](staticbroadcast.webp)

适用于全部Android平台。但存在如下几个缺点：

1） 广播接收器被管理软件、系统软件通过“自启管理”等功能禁用的场景无法接收到广播，从而无法自启。

2） 系统广播事件不可控，只能保证发生事件时拉活进程，但无法保证进程挂掉后立即拉活。

> Google已经开始意识到这些问题，所以在最新的Android N取消了 ACTION_NEW_PICTURE（拍照），ACTION_NEW_VIDEO（拍视频），CONNECTIVITY_ACTION（网络切换）等三种广播，无疑给了很多app沉重的打击。

因此，该方案主要作为备用手段。

## 利用第三方应用广播拉活

**利用不同的app进程使用广播来进行相互唤醒。举个3个比较常见的场景：**

- 场景1：接入第三方SDK也会唤醒相应的app进程，如微信sdk会唤醒微信，支付宝sdk会唤醒支付宝。由此发散开去，就会直接触发了下面的 场景3；
- 场景2：假如你手机里装了支付宝、淘宝、天猫、UC等阿里系的app，那么你打开任意一个阿里系的app后，有可能就顺便把其他阿里系的app给唤醒了。（只是拿阿里打个比方，其实BAT系都差不多）。

也可以通过反编译第三方 Top 应用，如：手机QQ、微信、支付宝、UC浏览器等，以及友盟、信鸽、个推等 SDK，找出它们外发的广播，在应用中进行监听，这样当这些应用发出广播时，就会将我们的应用拉活。

该方案的有效程度除与系统广播一样的因素外，主要受如下因素限制：

1） 反编译分析过的第三方应用的多少

2） 第三方应用的广播属于应用私有，当前版本中有效的广播，在后续版本随时就可能被移除或被改为不外发。

这些因素都影响了拉活的效果。

## 利用系统Service机制拉活

将 Service 设置为 START_STICKY，利用系统机制在 Service 挂掉后自动拉活：

``` java
@Override
public int onStartCommand(Intent intent, int flags, int startId) {
	return START_STICKY;
}
```

但是在以下两种情况无法拉活：

1. Service 第一次被异常杀死后会在5秒内重启，第二次被杀死会在10秒内重启，第三次会在20秒内重启，一旦在短时间内 Service 被杀死达到5次，则系统不再拉起。
2. 进程被取得 Root 权限的管理工具或系统工具通过 forestop 停止掉，无法重启。

# 参考资料

https://mp.weixin.qq.com/s/OXiFQNTyCHpqSP6B9HOiHw