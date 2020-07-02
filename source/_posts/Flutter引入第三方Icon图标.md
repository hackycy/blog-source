---
title: Flutter引入第三方Icon图标
date: 2020-07-02 14:26:06
tags:
    - Flutter
categories:
    - Flutter
---

**Flutter虽然内置了很多Icon图标，但是还是需要引入自己或第三方图标库提供的Icon。下面是解决办法。改文章以[阿里巴巴图标库](http://iconfont.cn/)为例。**

<!-- more -->

1、在阿里图标库选好需要用的图标，添加进购物车将选好的图标打包下载到本地（下载代码），复制`iconfont.ttf`文件到项目中。

2、将该文件放置于你的flutter项目下的`assets/fonts/`下。

3、打开项目根目录中的pubspec.yaml文件，在flutter中增加配置

``` yaml
# The following section is specific to Flutter.
flutter:

  # The following line ensures that the Material Icons font is
  # included with your application, so that you can use the icons in
  # the material Icons class.
  uses-material-design: true

  # An image asset can refer to one or more resolution-specific "variants", see
  # https://flutter.dev/assets-and-images/#resolution-aware.

  # For details regarding adding assets from package dependencies, see
  # https://flutter.dev/assets-and-images/#from-packages

  # To add custom fonts to your application, add a fonts section here,
  # in this "flutter" section. Each entry in this list should have a
  # "family" key with the font family name, and a "fonts" key with a
  # list giving the asset and other descriptors for the font. For
  # example:
  fonts:
    - family: Iconfont
      fonts:
         - asset: assets/fonts/iconfont.ttf
```

`Iconfont`为自定义名称，可自己定义。

4、在项目中使用：

``` dart
Icon(IconData(0xe621, fontFamily: 'iconfont'));
```

其中：`IconData()`里面，第一个参数为codePoint，代表图标字体存储的`Unicode`，这个可以在打开阿里巴巴库标题的下载文件中的HTML文件查看，**将 &# 字符替换为 0 即可，fontFamily：后面跟自定义的字体图标名称，我这里是Iconfont**

# 参考资料

https://blog.csdn.net/shuaizi96/article/details/88550217