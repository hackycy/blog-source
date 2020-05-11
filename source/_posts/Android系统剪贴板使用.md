---
title: Android系统剪贴板使用
date: 2019-07-04 10:19:05
keywords:
    - Android剪贴板
tags:
    - Android
categories:
    - Android
---

最近公司业务用到了关于Android系统剪贴板的使用，所以记录一下剪贴板的使用。

<!-- more -->

# ClipboardManager介绍

当需要使用到ClipboardManager时，需要把数据放在一个ClipData里，然后在把这个数据对象放在系统的剪贴板里面。

**ClipData有三种形式：**

- Text：文字字符串。文字是直接放在clip对象中，然后放在剪贴板里；粘贴这个字符串的时候直接从剪贴板拿到这个对象，把字符串放入你的应用存储中。
- URI：一个Uri对象。表示任何形式的URI。这种形式主要用于从一个content provider中复制复杂的数据。复制的时候把一个`Uri` 对象放在一个clip对象中，然后再放在剪贴板里；粘贴的时候取出这个clip对象，得到Uri，把它解析为一个数据资源比如content provider，然后从资源中复制数据到应用存储中。
- Intent：Intent对象。这支持了复制应用快捷方式。复制的时候把Intent对象放在clip对象中，再放入剪贴板；粘贴数据时，从clip对象中得到Intent对象，放入应用存储区域中。

> 注意：剪贴板里每次仅会持有一个ClipData对象，当应用再放入另一个ClipData对象进来时，前一个就消失了。

# 相关类

## ClipboardManager

ClipboardManager代表了系统的剪贴板，可以通过`(ClipboardManager) getSystemService(Context.CLIPBOARD_SERVICE)`系统服务来获取。

全名为**android.text.ClipboardManager**从API 11开始就废弃了。

取而代之的是它的子类：**android.content.ClipboardManager** (since API Level 11)。

## ClipData, ClipDescription, and ClipData.Item

前面说的clip对象就是**ClipData**类的对象，其中包含了一个 `ClipDescription`对象和一个或多个`ClipData.Item`对象。

**ClipDescription**对象中包含了一个数组，描述clip对象的MIME类型。

**ClipData.Item**对象中包含文字、URI或者Intent数据。**一个clip对象中可以包含一个或多个Item对象**。

比如你想要复制list中的多项数据，你可以为list中的每一项创建一个**ClipData.Item**对象，然后把它们放进一个**ClipData**对象中，这样就一次性把多项数据都放在了剪贴板中。

> 注意ClipData这个类是API 11才有的。

## ClipData中的简洁方法

- **ClipData**类中有一些静态的简洁方法，用于创建只有一个**ClipData.Item**和一条简单描述( `ClipDescription`)的ClipData对象。

- newPlainText(label, text)返回ClipData对象，数据是文字text，描述是label，MIME类型是`MIMETYPE_TEXT_PLAIN`。

- newUri(resolver, label, URI)

- newIntent(label, intent)
- newHtmlText(label,text, htmlText)
- newRawUri(label, uri)

> newHtmlText method need Call requires API level 16

# ClipboardFramework架构图

![](clipboardframework.png)

# ClipboardManager使用

``` java
//获取剪贴板服务：
ClipboardManager manager = (ClipboardManager) getSystemService(Context.CLIPBOARD_SERVICE);
//然后把数据放在ClipData对象中
ClipData data = ClipData.newPlainText("label", "this is clipboard data");       
manager.setPrimaryClip(data);
```

上面的代码就可以将文字放到剪贴板了，然后找个输入框“粘贴”就行啦。

> 如果需要自由复制TextView等控件的文字，只要在该控件上添加`android:textIsSelectable="true"`或者使用代码设置`setTextIsSelectable(true)`即可。

**URI:**

``` java
// Creates a Uri based on a base Uri and a record ID based on the contact's last name
// Declares the base URI string
private static final String CONTACTS = "content://com.example.contacts";

// Declares a path string for URIs that you use to copy data
private static final String COPY_PATH = "/copy";

// Declares the Uri to paste to the clipboard
Uri copyUri = Uri.parse(CONTACTS + COPY_PATH + "/" + lastName);

//...

// Creates a new URI clip object. The system uses the anonymous getContentResolver() object to
// get MIME types from provider. The clip object's label is "URI", and its data is
// the Uri previously created.
ClipData clip = ClipData.newUri(getContentResolver(),"URI",copyUri);
```

**Intent:**

``` java
// Creates the Intent
Intent appIntent = new Intent(this, com.example.demo.myapplication.class);

//...

// Creates a clip object with the Intent in it. Its label is "Intent" and its data is
// the Intent object created previously
ClipData clip = ClipData.newIntent("Intent",appIntent);
```

# 获取剪贴板内容

``` java
ClipboardManager manager = (ClipboardManager) getSystemService(Context.CLIPBOARD_SERVICE);

String resultString = "";

//检查剪贴板是否有内容
if(manager.hasPrimaryClip()){
		ClipData clipData = manager.getPrimaryClip();
		int count = clipData.getItemCount();

		for (int i = 0; i < count; ++i) {

				ClipData.Item item = clipData.getItemAt(i);
				CharSequence str = item.coerceToText(MainActivity.this);


				resultString += str;
		}
}
Log.e("TAG", resultString);
```

# 总结

- 任何时间，都只有一个clip对象在剪贴板里。新的复制操作都会覆盖前一个clip对象，因为用户可能从你的应用中退出，从其他应用中拷贝一个东西，所以你不能假定用户在你的应用中拷贝的上一个东西一定还放在剪贴板里。

-  一个clip对象，即ClipData中的多个`ClipData.Item` 对象是为了支持多选项的复制粘贴，而不是为了支持单选的多种形式。你通常需要clip对象中的所有的项目，即[ClipData.Item](http://developer.android.com/reference/android/content/ClipData.Item.html)有一样的形式，比如都是文字，都是URI或都是Intent，而不是混合各种形式。

- 当你提供数据时，你可以提供不同的MIME表达方式。将你支持的MIME类型加入到**ClipDescription**中去，然后在你的content provider中实现它。

- 从剪贴板得到数据时，你的应用有责任检查可用的MIME类型，然后决定使用哪一个。即便有一个clip对象在剪贴板中并且用户要求粘贴，你的应用有可能不需要进行粘贴操作。你应该在MIME类型兼容的时候执行粘贴操作。你可以选择使用 `coerceToText()`方法将粘贴的内容转换为文字。如果你的应用支持多种类型，你可以让用户自己选用哪一个。

# 参考资料

http://developer.android.com/guide/topics/text/copy-paste.html

https://www.cnblogs.com/zhujiabin/p/7605553.html