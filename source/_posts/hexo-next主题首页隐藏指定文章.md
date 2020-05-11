---
title: hexo next主题首页隐藏指定文章
date: 2019-04-23 12:24:19
keywords:
    - Hexo
    - 博客
tags:
    - hexo
categories:
    - hexo
---

因为博客下混合了我技术类和生活类的文章，但是首页我只想显示技术类，所以记录下做法。

<!-- more -->

**具体做法**

找到`themes⁩` ▸ `⁨next`⁩ ▸ `⁨layout`⁩文件夹下的`index.swig`文件

![](index1.png)

定位修改`post_template.render`

修改成以下代码,其中修改的是为文章的首页显示添加判断条件

``` swig
{% if post.visible !== 'hide' %}
   {{ post_template.render(post, true) }}
{% endif %}
```

即修改后

![](index2.png)

**在新的post中添加visible字段来控制是否首页显示**

``` md
---
title: post title
date: 2019-04-23 12:24:19
tags:
    - hexo
categories:
    - hexo
visible: hide
---

.........

```




