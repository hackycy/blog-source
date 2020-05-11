---
title: Android Toolbar 使用详解
date: 2019-06-27 10:59:14
tags:
    - Android
categories:
    - Android
---

# 初识Toolbar

**Toolbar** 是在 Android 5.0 开始推出的一个 Material Design 风格的导航控件 ，Google 非常推荐大家使用 **Toolbar** 来作为Android客户端的导航栏，以此来取代之前的 **Actionbar** 。

<!-- more -->

与 **Actionbar** 相比，**Toolbar** 明显要灵活的多。它不像 **Actionbar** 一样，一定要固定在Activity的顶部，而是可以放到界面的任意位置。除此之外，在设计 **Toolbar** 的时候，Google也留给了开发者很多可定制修改的余地，这些可定制修改的属性在API文档中都有详细介绍，如：

- **设置导航栏图标；**
- **设置App的logo；**
- **支持设置标题和子标题；**
- **支持添加一个或多个的自定义控件；**
- **支持Action Menu；**

# 使用Toolbar

前面提到 **Toolbar** 是在 Android 5.0 才开始加上的，Google 为了将这一设计向下兼容，自然也少不了要推出兼容版的 **Toolbar** 。为此，我们需要在工程中引入 **appcompat-v7** 的兼容包，使用 **android.support.v7.widget.Toolbar** 进行开发。但是由于**support**库现在google团队已经不在维护了，最新版本好像是28点多，已经迁移到了**AndroidX**，所以该篇文章使用**AndroidX**，只是包名相对的变化了，使用和v7包没有多大变化。

先来看看运行效果

![Toolbar演示](toolbar_show.png)

按照效果图，从左到右分别是我们前面提及到的 **导航栏图标**、**App的logo**、**标题和子标题**、**自定义控件**、以及 **ActionMenu** 。接着，我们来看下布局文件和代码实现。

首先，在布局文件 `activity_main.xml `中添加进我们需要的 Toolbar 控件

``` xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">
    <androidx.appcompat.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:background="@color/colorPrimary"
        android:layout_height="?attr/actionBarSize">
        <TextView
            android:text="textview"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />
    </androidx.appcompat.widget.Toolbar>
</LinearLayout>
```

接着创建一个`toolbar_menu.xml`菜单项

``` xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <item
        android:id="@+id/action_search"
        android:icon="@android:drawable/ic_input_add"
        android:title="menu_search"
        app:showAsAction="ifRoom" />

    <item
        android:id="@+id/action_notification"
        android:icon="@android:drawable/ic_delete"
        android:title="menu_notifications"
        app:showAsAction="ifRoom" />

    <item
        android:id="@+id/action_item1"
        android:title="item_01"
        app:showAsAction="never" />

    <item
        android:id="@+id/action_item2"
        android:title="item_02"
        app:showAsAction="never" />
</menu>
```

> 我的activity继承自AppCompatActivity，并不是原生sdk内部的，因此不能使用`android:showAsAction`，否则会报错。所以需要使用自定义的命名空间app。
>
> ifRoom表示有空间则显示，never表示从不显示，而是会通过overflowwindow显示。

最后在`MainActivity`中调用

``` java
public class MainActivity extends AppCompatActivity {

    private Toolbar mToolbar;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mToolbar = findViewById(R.id.toolbar);

        mToolbar.setTitle("标题");
        mToolbar.setSubtitle("子标题");
        mToolbar.setNavigationIcon(android.R.drawable.ic_menu_info_details);
        mToolbar.setLogo(android.R.drawable.ic_menu_view);
        setSupportActionBar(mToolbar); //最后设置
    }

  	/**
  		菜单栏目
  	*/
    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.toolbar_menu, menu);
        return super.onCreateOptionsMenu(menu);
    }
}
```

更改主题：

为了能够正常使用ToolBar，我们需要隐藏原来的ActionBar，这个可以在主题中修改

``` xml
<item name="windowNoTitle">true</item>
<item name="windowActionBar">false</item>
```

或者继承某个父类:`Theme.AppCompat.Light.NoActionBar`

也可以在代码中

如果是`AppCompatActivity`

``` java
supportRequestWindowFeature(Window.FEATURE_NO_TITLE);
//setContentView之前调用，否则报错
```

如果是`Activity`

``` java
requestWindowFeature(Window.FEATURE_NO_TITLE);
```

> 最后运行就可以有上面图中的效果啦。

# 注意事项

- 如果你想修改标题和子标题的字体大小、颜色等，可以调用**setTitleTextColor**、**setTitleTextAppearance**、**setSubtitleTextColor**、**setSubtitleTextAppearance** 这些API；
- 自定义的View位于 **title**、**subtitle** 和 **actionmenu** 之间，这意味着，如果 **title** 和 **subtitle** 都在，且 **actionmenu选项** 太多的时候，留给自定义View的空间就越小；
- **导航图标** 和 **app logo** 的区别在哪？如果你只设置 **导航图标**（ or **app logo**） 和 **title**、**subtitle**，会发现 **app logo** 和 **title**、**subtitle** 的间距比较小，看起来不如 **导航图标** 与 它们两搭配美观；
- **Toolbar** 和其他控件一样，很多属性设置方法既支持代码设置，也支持在xml中设置
- **如果只是当成一个普通控件使用，即不调用`setSupportActionBar(mToolbar);`方法，可以使用`mToolbar.inflateMenu`方法来设置菜单。**

# 其他样式修改

## 修改Toolbar popup menu样式

点击右上角的三个点，会弹出一个popup menu，如下所示：

![](popup_show.png)

可以看到弹出菜单的样式是白底黑字，那么有没有办法改变它的背景颜色呢，使得菜单显示为黑底白字。这肯定是有的。

在`styles.xml`文件中新建一个主题

``` xml
<!-- toolbar弹出菜单样式 -->
<style name="ToolbarPopupTheme" parent="@style/ThemeOverlay.AppCompat.Dark">   
     <item name="android:colorBackground">#000000</item>
</style>
```

可以看到这个主题的parent是直接继承自`ThemeOverlay.AppCompat.Dark`，是支持包的一个主题，并且我们在内部声明了`android:colorBackground`这个属性，我们只要更改这个属性就能变更菜单的背景颜色了。接下来我们在布局文件中引入这个主题，这也很简单，为toolbar添加额外的属性如下:

``` xml
app:popupTheme="@style/ToolbarPopupTheme"
```

运行效果：

![](popup_show2.png)

## 修改Toolbar popup menu 弹出位置

在上图看到，弹出的菜单的位置是过于偏上的，我们可以修改它的位置，让它在toolbar的下面，这样看起来也美观一些：

修改`styles.xml`文件，添加

``` xml
<!-- toolbar弹出菜单样式 -->
<style name="ToolbarPopupTheme" parent="@style/ThemeOverlay.AppCompat.Dark">
    <item name="android:colorBackground">#000000</item>
    <item name="actionOverflowMenuStyle">@style/OverflowMenuStyle</item> 
  	<!--新增一个item，用于控制menu-->
</style>

<style name="OverflowMenuStyle" parent="Widget.AppCompat.Light.PopupMenu.Overflow">
		<item name="overlapAnchor">false</item>  
  	<!--把该属性改为false即可使menu位置位于toolbar之下-->
</style>
```

布局文件中引用该主题

``` xml
app:popupTheme="@style/ToolbarPopupTheme"
```

运行效果：

![](popup_show3.png)

## 修改Action Menu Item 的文字颜色

在`styles.xml`文件中，在popup menu的主题添加：

``` xml
<!-- toolbar弹出菜单样式 -->
<style name="ToolbarPopupTheme" parent="@style/ThemeOverlay.AppCompat.Dark">
		<item name="android:colorBackground">#000000</item>
		<item name="actionOverflowMenuStyle">@style/OverflowMenuStyle</item> <!--新增一个item，用于控制menu-->
		<item name="android:textColorPrimary">@color/colorPrimary</item>
</style>
```

布局文件中引用该主题

```xml
app:popupTheme="@style/ToolbarPopupTheme"
```

运行效果：

![](popup_show4.png)

# 踩坑

## 设置监听事件不生效

看一下代码

``` java
private void initToolbar(){
		mToolbar = findViewById(R.id.toolbar);
		mToolbar.setTitle("");
		mToolbar.setNavigationOnClickListener(new View.OnClickListener() {
				@Override
				public void onClick(View view) {
						//。。。
				}
		});
		mToolbar.setOnMenuItemClickListener(new Toolbar.OnMenuItemClickListener() {
		@Override
				public boolean onMenuItemClick(MenuItem item) {
						//。。。
						return true;
				}
		});
		setSupportActionBar(mToolbar);
}
```

代码正常设置监听但是就是监听事件就是没有触发，查询后，监听事件必须在`setSupportActionBar(mToolbar);`之后设置。

``` java
private void initToolbar(){
		mToolbar = findViewById(R.id.toolbar);
		mToolbar.setTitle("");
		setSupportActionBar(mToolbar);
  	mToolbar.setNavigationOnClickListener(new View.OnClickListener() {
				@Override
				public void onClick(View view) {
						//。。。
				}
		});
		mToolbar.setOnMenuItemClickListener(new Toolbar.OnMenuItemClickListener() {
		@Override
				public boolean onMenuItemClick(MenuItem item) {
						//。。。
						return true;
				}
		});
}
```

调换一下位置即可正常运行。

# 总结

以上都是一些基本的使用，Toolbar控件的使用还是很灵活的，还有很多一些高级技巧，比如配合上状态栏变成沉浸式，或者配合`CoordinatorLayout`等实现更炫的效果。

# 参考资料

https://www.jianshu.com/p/79604c3ddcae

https://www.jianshu.com/p/ae0013a4f71a