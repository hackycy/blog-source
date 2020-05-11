---
title: Android 软键盘imeOptions事件响应
date: 2019-07-31 12:17:22
keywords:
    - Android
tags:
    - Android
    - EditText
categories:
    - Android
---

在一些登陆，搜索的过程中，输入框在用户输入完后点击软键盘的回车、搜索等的按钮即可完成登陆或者搜索的功能，不必要再让用户再次点击页面上的登陆或者搜索按钮才能够进行操作。

<!-- more -->

# 概述

用图来说话吧，下图是一个高德地图的一个搜索框：

![](demo.png)

你们会发现并没有搜索按钮，而是在键盘的右下角有一个小图标(放大镜图标或者搜索字样)，这代表的是搜索的动作，点击后就可以进行搜索。

而在一个浏览器下的一个输入框时，软键盘也会有相对应的变化，如下图：

![](demo2.png)

填写网址后点击软键盘的转到即可到达输入的网页。

# imeOptions

上面概述上的两个功能使得EditText拥有图标变化的能力就是`android:imeOptions`属性。

而EditorInfo类源码中一共提供了以下属性供我们选择

``` java
public static final int IME_ACTION_DONE = 6; //对应actionDone
public static final int IME_ACTION_GO = 2;  //对应actionGo
public static final int IME_ACTION_NEXT = 5;  //对应actionNext
public static final int IME_ACTION_NONE = 1;  //对应actionNone
public static final int IME_ACTION_PREVIOUS = 7;  //对应actionPrevious
public static final int IME_ACTION_SEARCH = 3;  //对应actionSearch
public static final int IME_ACTION_SEND = 4;  //对应actionSearch
public static final int IME_ACTION_UNSPECIFIED = 0;  //对应actionUnspecified
```

我们在xml中编写对应的EditText，来直接演示下理解它们的实用意义：

``` xml
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <EditText
            android:id="@+id/actionGo"
            android:layout_width="match_parent"
            android:imeOptions="actionGo"
            android:hint="actionGo"
            android:singleLine="true"
            android:layout_height="45dp"/>

        <EditText
            android:id="@+id/actionSearch"
            android:layout_width="match_parent"
            android:imeOptions="actionSearch"
            android:hint="actionSearch"
            android:singleLine="true"
            android:layout_height="45dp"/>

        <EditText
            android:id="@+id/actionNext"
            android:layout_width="match_parent"
            android:imeOptions="actionNext"
            android:hint="actionNext"
            android:singleLine="true"
            android:layout_height="45dp"/>

        <EditText
            android:id="@+id/actionNone"
            android:layout_width="match_parent"
            android:imeOptions="actionNone"
            android:hint="actionNone"
            android:singleLine="true"
            android:layout_height="45dp"/>

        <EditText
            android:id="@+id/actionDone"
            android:layout_width="match_parent"
            android:imeOptions="actionDone"
            android:hint="actionDone"
            android:singleLine="true"
            android:layout_height="45dp"/>

        <EditText
            android:id="@+id/actionSend"
            android:layout_width="match_parent"
            android:imeOptions="actionSend"
            android:hint="actionSend"
            android:singleLine="true"
            android:layout_height="45dp"/>

        <EditText
            android:id="@+id/actionPrevious"
            android:layout_width="match_parent"
            android:imeOptions="actionPrevious"
            android:hint="actionPrevious"
            android:singleLine="true"
            android:layout_height="45dp"/>

        <EditText
            android:id="@+id/actionUnspecified"
            android:layout_width="match_parent"
            android:imeOptions="actionUnspecified"
            android:hint="actionUnspecified"
            android:singleLine="true"
            android:layout_height="45dp"/>
    </LinearLayout>
</ScrollView>
```

运行效果：

![](demo3.png)

> **注意：该属性必须配置`android:singleLine="true"`属性或者配合`android:maxLines="1"`与`android:inputType="number"`结合使用。缺一不可。即便singleLine已经被废弃，又或者单纯使用maxLines是无效的。**

对EditText指定不同的imeOptions之后，就需要实现OnEditorActionListener 中的onEditorAction()方法，然后根据不同的动作执行进行响应。 

对于actionDone、actionNext和actionPrevious，系统都自己进行了部分处理。 
- actionDone：隐藏输入法 
- actionNext：跳到下一个EditText 
- actionPrevious：跳到上一个EditText

我们使用案例中实现以一个搜索的例子实现：

``` java
public class MainActivity extends AppCompatActivity {

    private EditText actionSearch;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        actionSearch = findViewById(R.id.actionSearch);

        actionSearch.setOnEditorActionListener(new TextView.OnEditorActionListener() {
            @Override
            public boolean onEditorAction(TextView textView, int actionId, KeyEvent keyEvent) {
                if(actionId == EditorInfo.IME_ACTION_SEARCH) {
                    if(actionSearch.getText().toString().isEmpty()) {
                        Toast.makeText(MainActivity.this, "内容不能为空", Toast.LENGTH_SHORT).show();
                    } else {
                        Toast.makeText(MainActivity.this, "您搜索的内容是：" + actionSearch.getText().toString(), Toast.LENGTH_SHORT).show();
                    }
                    return true;
                }
                //只负责搜索事件处理，其他处理交回到系统的默认处理
                return false;
            }
        });

    }
}
```

运行效果：

![](demo4.png)

> 部分第三方的输入法，对EditorInfo支持的不一样，有的功能实现了，但是对应的图标没有修改过来，有的干脆功能就没有实现。

# 总结

`actionDone`  - 完成 - 对应 `EditorInfo.IME_ACTION_DONE `
`actionGo` - 前进 - 对应 `EditorInfo.IME_ACTION_GO `
`actionNext` - 下一项 - 对应 `EditorInfo.IME_ACTION_NEXT `
`actionNone` - 无动作 - 对应 `EditorInfo.IME_ACTION_NONE `
`actionPrevious` - 上一项 - 对应 `EditorInfo.IME_ACTION_PREVIOUS` 
`actionSearch` - 搜索 - 对应 `EditorInfo.IME_ACTION_SEARCH `
`actionUnspecified` - 未指定 - `对应 EditorInfo.IME_ACTION_UNSPECIFIED` 
`actionSend` - 发送 - 对应 `EditorInfo.IME_ACTION_SEND`

# 参考资料

https://blog.csdn.net/liuweiballack/article/details/46708697

https://www.jianshu.com/p/d5c419bb4e19