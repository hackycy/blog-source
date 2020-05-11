---
title: Android TextView富文本之Html标签
date: 2020-03-19 11:59:58
keywords:
    - Android
tags:
    - Android
categories:
    - Android
---

# 前言

TextView作为一个我们经常使用到的控件，但是在写一些UI的时候会遇到一些特别的情况，来看一下示例图：

<!-- more -->

![](anli.png)

券的价格是需要突出显示的，还有原价也需要显示一个删除线。

# TextView的多样化显示

如果是显示不同样的大小文本字体颜色可以直接使用SpannableString实现。但是这里使用HTML标签来实现效果。

例如图上的券后文本，可以使用：

``` java
mTextView.setText(Html.fromHtml("<small>券后仅</small><big> 20 </big><small>元</small>"));
```

如果在strings.xml中定义时候需要注意，需要转义HTML标签，不然的话 经过Android处理后 所有的HTML标签都给过滤掉了。需要把所有的“<”用“&lt;”替换，例如： 

`<string name="htmlText">&lt;strong>粗体&lt;/strong></string>`

如果文本内容比较长， 则转义起来比较麻烦，并且阅读也不太方便，这种情况下可以使用XML的CDATA标记， 如下所示：

``` xml
<string name="discount_price2"><![CDATA[<small>券后仅</small><big> %1$s </big><small>元</small>]]></string>
```

> strings.xml中节点是支持占位符的，如下所示：
>
> `<string name="data">整数型:%1$d，浮点型：%2$.2f，字符串:%3$s</string>`
>
> 其中%后面是占位符的位置，从1开始，
>
> **$ 后面是填充数据的类型**
>
> - $d：表示整数型；
> - $f ：表示浮点型，其中f前面的.2表示小数的位数
> - $s：表示字符串
>
> 然后使用`Context.getResources().getString(data, ...format)`即可

下面是一些常用的HTML标签。

| Tags             | Format              |
| :--------------- | :------------------ |
| b, strong        | Bold                |
| i, em, cite, dfn | Italics             |
| u                | Underline           |
| sub              | Subtext             |
| sup              | Supertext           |
| big              | Big                 |
| small            | Small               |
| tt               | Monospace           |
| h1 … h6          | Headlines           |
| img              | Image               |
| font             | Font face and color |
| blockquote       | For longer quotes   |
| a                | Link                |
| div, p           | Paragraph           |
| br               | Linefeed            |

中文版：

| 标签                                       | 说明                                                         |
| ------------------------------------------ | ------------------------------------------------------------ |
| `<br>`                                     | 插入一个换行符，标签是空标签                                 |
| `<p>`                                      | 定义段落，标签会自动在其前后各添加一个空行                   |
| `<h1>`                                     | h1标题                                                       |
| `<h2>`                                     | h2标题                                                       |
| `<h3>`                                     | h3标题                                                       |
| `<h4>`                                     | h4标题                                                       |
| `<h5>`                                     | h5标题                                                       |
| `<h6>`                                     | h6标题                                                       |
| `<div>`                                    | 文档分节                                                     |
| `<strong>`                                 | 把文本定义为语气更强的强调的内容。TextView中表现为文本加粗   |
| `<b>`                                      | 文本加粗                                                     |
| `<em>`                                     | 把文本定义为强调的内容。TextView中表现为斜体文本效果。       |
| `<cite>`                                   | 定义引用，TextView中表现为斜体文本效果                       |
| `<dfn>`                                    | 标记那些对特殊术语或短语的定义。TextView中表现为斜体文本效果。 |
| `<i>`                                      | 显示斜体文本效果                                             |
| `<big>`                                    | 呈现大号字体效果                                             |
| `<small>`                                  | 呈现小号字体效果                                             |
| `<strike>`                                 | 定义删除线样式的文字                                         |
| `<font size="..." color="..." face="...">` | 规定文本的字体、字体尺寸、字体颜色   color：文本颜色；size：文本大小；face：文本字体 |
| `<blockquote>`                             | 将`<blockquote>` 与 `</blockquote> `之间的文本从常规文本中分离出来,通常在左、右两边进行缩进，有时使用斜体 |
| `<tt>`                                     | 呈现类似打字机或者等宽的文本效果                             |
| `<a>`                                      | 定义超链接。最重要的属性是 href 属性，它指示链接的目标。 href：指示链接的目标 |
| `<u>`                                      | 为文本添加下划线                                             |
| `<sup>`                                    | 定义上标文本                                                 |
| `<sub>`                                    | 定义下标文本                                                 |
| `<img src="...">`                          | 向网页中嵌入一幅图像。`<img>`标签并不会在网页中插入图像，而是从网页上链接图像。`<img> ` 标签创建的是被引用图像的占位空间。   src：图像的url；alt：图像的替代文本 |

# strike标签使用注意

在使用`<strike>`时，6.0系统可能会不显示。则需要在代码中使用：

``` java
//添加删除线      
tv.getPaint().setFlags(Paint.STRIKE_THRU_TEXT_FLAG);  
//在代码中设置加粗            
tv.getPaint().setFlags(Paint.FAKE_BOLD_TEXT_FLAG);  
//添加下划线        
tv.getPaint().setFlags(Paint.UNDERLINE_TEXT_FLAG);
```

要什么效果可以自己在代码中设置，选择不同的 Flags 就行了。

# a标签使用注意

使用a标签后设置了href属性后，不做任何设置是无法进行点击跳转的，需要TextView解析设置一下：

``` java
tv.setText(Html.fromHtml(url));//解析html
tv.setMovementMethod(LinkMovementMethod.getInstance());//设置可点击
```

或着xml中添加

``` xml
android:autoLink="web"
```

这样默认是用浏览器打开a标签里面的链接。

**但是如果要获取到a标签的点击事件和链接的话，那么就要换一个方法：**

> 其他方法基本都要涉及到SpannableString的设置；或是自定义UrlSpan，重写它的onClick方法；有些还要遍历文本寻找以http开头的字符串。但是想要找一个比较简单些的方法，所以用了一下方法实现

而该方法使用参考了LinkMovementMethod来实现

``` java
import android.content.Context;
import android.content.Intent;
import android.text.Layout;
import android.text.Selection;
import android.text.Spannable;
import android.text.method.LinkMovementMethod;
import android.text.method.MovementMethod;
import android.text.style.URLSpan;
import android.view.MotionEvent;
import android.widget.TextView;

import tv.xianqi.test190629.view.activity.WebViewActivity;

public class WebLinkMethod extends LinkMovementMethod {

    private static WebLinkMethod instance;
    private Context context;

    private WebLinkMethod(Context context) {
        this.context = context;
    }

    public static MovementMethod getInstance(Context context) {
        if (instance == null)
            instance = new WebLinkMethod(context);
        return instance;
    }

    @Override
    public boolean onTouchEvent(TextView widget, Spannable buffer, MotionEvent event) {
        int action = event.getAction();

        if (action == MotionEvent.ACTION_UP ||
                action == MotionEvent.ACTION_DOWN) {
            int x = (int) event.getX();
            int y = (int) event.getY();

            x -= widget.getTotalPaddingLeft();
            y -= widget.getTotalPaddingTop();

            x += widget.getScrollX();
            y += widget.getScrollY();

            Layout layout = widget.getLayout();
            int line = layout.getLineForVertical(y);
            int off = layout.getOffsetForHorizontal(line, x);
//          ClickableSpan[] link = buffer.getSpans(off, off, ClickableSpan.class);
            URLSpan[] link = buffer.getSpans(off, off, URLSpan.class);
            if (link.length != 0) {
                if (action == MotionEvent.ACTION_UP) {
//                  link[0].onClick(widget);
//                  这里改为我们需要做的动作
                    WebViewActivity.startWebViewActivity(context, link[0].getURL());
                } else if (action == MotionEvent.ACTION_DOWN) {
                    Selection.setSelection(buffer,
                            buffer.getSpanStart(link[0]),
                            buffer.getSpanEnd(link[0]));
                }
                return true;
            } else {
                Selection.removeSelection(buffer);
            }
        }
        return super.onTouchEvent(widget, buffer, event);
    }

}
```

使用时则改用：

``` java
textView.setMovementMethodCompat(WebLinkMethod.getInstance(this));
```

由于页面跳转我们需要用到原页面的上下文，于是修改了构造函数，加入了Context；

URLSpan是ClickableSpan的子类，实现了getURL()的方法，所以这里要换成它我们才能取到链接地址，link[0]就是我们点击到的超链接字符串，通过link[0].getURL()我们可以获得它的链接地址。

# 参考链接

https://blog.csdn.net/github_37847975/article/details/75425633

https://blog.csdn.net/bhadx520/article/details/47433203?depth_1-utm_source=distribute.pc_relevant_right.none-task&utm_source=distribute.pc_relevant_right.none-task

https://blog.csdn.net/ganggang1st/article/details/6804086

https://blog.csdn.net/yulyu/article/details/52244925

https://www.jianshu.com/p/c4e1aacb9685