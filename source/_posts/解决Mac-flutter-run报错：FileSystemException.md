---
title: 解决Mac flutter run报错：FileSystemException
date: 2020-06-05 10:44:53
tags:
    - Flutter
categories:
    - Flutter
---

在正式学习完Dart的路程下，兴奋的配置完成Flutter的环境后，想要跑一个demo的时候坑又来了一个。

<!-- more -->

# 环境配置

**flutter doctor**无问题

![](https://github.static.si-yee.com/posts/flutterrunFileSystemExceptionError/20200605104644.png)

但是在**flutter run**后出现了该错误：

![](https://github.static.si-yee.com/posts/flutterrunFileSystemExceptionError/20200605104915.png)

出现这些问题，查看了Flutter的很多issues，有的说权限，有的说配置国内源。的确如此，但是先确认好步骤。

# 解决

如果安装好了SDK先删除重新配置吧。环境配置的变量可以不用删除。重复安装sdk的步骤就可以了。**记得先配好环境变量再运行Flutter的相关命令**。特别是配置国内源。[官方教程](https://flutter.dev/docs/get-started/install/macos)

配置`.bash_profile`，如果是使用`zsh`下，每次都需要打开终端后输入`source ~/.bash_profile`，解决方法就是在`.zshrc`文件配置`source ~/.bash_profile`命令即可。

``` bash
export PUB_HOSTED_URL=https://mirrors.tuna.tsinghua.edu.cn/dart-pub
export FLUTTER_STORAGE_BASE_URL=https://mirrors.tuna.tsinghua.edu.cn/flutter
export PATH="$PATH:/Users/zjyzy/Library/Flutter/bin"
```

> 如果你有科学上网，可以配置代理：
>
> `export http_proxy=http://127.0.0.1:1087`和`export https_proxy=http://127.0.0.1:1087`

重新配置好SDK后，配置好`flutter precache`和`flutter doctor`无误后，配置Flutter SDK的目录权限。

``` bash
$ sudo chmod -R 777 /Users/zjyzy/Library/Flutter/bin
```

使用`flutter create`命令创建项目，**这里要记住，一定不要使用sudo创建项目**

``` bash
$ flutter create flutterdemo
Creating project flutterdemo...
  flutterdemo/ios/Runner.xcworkspace/contents.xcworkspacedata (created)
  flutterdemo/ios/Runner.xcworkspace/xcshareddata/IDEWorkspaceChecks.plist (created)
  flutterdemo/ios/Runner.xcworkspace/xcshareddata/WorkspaceSettings.xcsettings (created)
  flutterdemo/ios/Runner/Info.plist (created)
  flutterdemo/ios/Runner/Assets.xcassets/LaunchImage.imageset/LaunchImage@2x.png (created)
  flutterdemo/ios/Runner/Assets.xcassets/LaunchImage.imageset/LaunchImage@3x.png (created)
  flutterdemo/ios/Runner/Assets.xcassets/LaunchImage.imageset/README.md (created)
  flutterdemo/ios/Runner/Assets.xcassets/LaunchImage.imageset/Contents.json (created)
  flutterdemo/ios/Runner/Assets.xcassets/LaunchImage.imageset/LaunchImage.png (created)
  flutterdemo/ios/Runner/Assets.xcassets/AppIcon.appiconset/Icon-App-76x76@2x.png (created)
  flutterdemo/ios/Runner/Assets.xcassets/AppIcon.appiconset/Icon-App-29x29@1x.png (created)
  flutterdemo/ios/Runner/Assets.xcassets/AppIcon.appiconset/Icon-App-40x40@1x.png (created)
  flutterdemo/ios/Runner/Assets.xcassets/AppIcon.appiconset/Icon-App-20x20@1x.png (created)
  flutterdemo/ios/Runner/Assets.xcassets/AppIcon.appiconset/Icon-App-1024x1024@1x.png (created)
  flutterdemo/ios/Runner/Assets.xcassets/AppIcon.appiconset/Icon-App-83.5x83.5@2x.png (created)
  flutterdemo/ios/Runner/Assets.xcassets/AppIcon.appiconset/Icon-App-20x20@3x.png (created)
  flutterdemo/ios/Runner/Assets.xcassets/AppIcon.appiconset/Contents.json (created)
  flutterdemo/ios/Runner/Assets.xcassets/AppIcon.appiconset/Icon-App-20x20@2x.png (created)
  flutterdemo/ios/Runner/Assets.xcassets/AppIcon.appiconset/Icon-App-29x29@3x.png (created)
  flutterdemo/ios/Runner/Assets.xcassets/AppIcon.appiconset/Icon-App-40x40@2x.png (created)
  flutterdemo/ios/Runner/Assets.xcassets/AppIcon.appiconset/Icon-App-60x60@3x.png (created)
  flutterdemo/ios/Runner/Assets.xcassets/AppIcon.appiconset/Icon-App-60x60@2x.png (created)
  flutterdemo/ios/Runner/Assets.xcassets/AppIcon.appiconset/Icon-App-76x76@1x.png (created)
  flutterdemo/ios/Runner/Assets.xcassets/AppIcon.appiconset/Icon-App-40x40@3x.png (created)
  flutterdemo/ios/Runner/Assets.xcassets/AppIcon.appiconset/Icon-App-29x29@2x.png (created)
  flutterdemo/ios/Runner/Base.lproj/LaunchScreen.storyboard (created)
  flutterdemo/ios/Runner/Base.lproj/Main.storyboard (created)
  flutterdemo/ios/Runner.xcodeproj/project.xcworkspace/contents.xcworkspacedata (created)
  flutterdemo/ios/Runner.xcodeproj/project.xcworkspace/xcshareddata/IDEWorkspaceChecks.plist (created)
  flutterdemo/ios/Runner.xcodeproj/project.xcworkspace/xcshareddata/WorkspaceSettings.xcsettings (created)
  flutterdemo/ios/Runner.xcodeproj/xcshareddata/xcschemes/Runner.xcscheme (created)
  flutterdemo/ios/Flutter/Debug.xcconfig (created)
  flutterdemo/ios/Flutter/Release.xcconfig (created)
  flutterdemo/ios/Flutter/AppFrameworkInfo.plist (created)
  flutterdemo/ios/.gitignore (created)
  flutterdemo/test/widget_test.dart (created)
  flutterdemo/flutterdemo.iml (created)
  flutterdemo/.gitignore (created)
  flutterdemo/.metadata (created)
  flutterdemo/android/app/src/profile/AndroidManifest.xml (created)
  flutterdemo/android/app/src/main/res/mipmap-mdpi/ic_launcher.png (created)
  flutterdemo/android/app/src/main/res/mipmap-hdpi/ic_launcher.png (created)
  flutterdemo/android/app/src/main/res/drawable/launch_background.xml (created)
  flutterdemo/android/app/src/main/res/mipmap-xxxhdpi/ic_launcher.png (created)
  flutterdemo/android/app/src/main/res/mipmap-xxhdpi/ic_launcher.png (created)
  flutterdemo/android/app/src/main/res/values/styles.xml (created)
  flutterdemo/android/app/src/main/res/mipmap-xhdpi/ic_launcher.png (created)
  flutterdemo/android/app/src/main/AndroidManifest.xml (created)
  flutterdemo/android/app/src/debug/AndroidManifest.xml (created)
  flutterdemo/android/gradle/wrapper/gradle-wrapper.properties (created)
  flutterdemo/android/gradle.properties (created)
  flutterdemo/android/.gitignore (created)
  flutterdemo/android/settings.gradle (created)
  flutterdemo/android/app/build.gradle (created)
  flutterdemo/android/app/src/main/kotlin/com/example/flutterdemo/MainActivity.kt (created)
  flutterdemo/android/build.gradle (created)
  flutterdemo/android/flutterdemo_android.iml (created)
  flutterdemo/pubspec.yaml (created)
  flutterdemo/README.md (created)
  flutterdemo/ios/Runner/Runner-Bridging-Header.h (created)
  flutterdemo/ios/Runner/AppDelegate.swift (created)
  flutterdemo/ios/Runner.xcodeproj/project.pbxproj (created)
  flutterdemo/lib/main.dart (created)
  flutterdemo/.idea/runConfigurations/main_dart.xml (created)
  flutterdemo/.idea/libraries/Flutter_for_Android.xml (created)
  flutterdemo/.idea/libraries/Dart_SDK.xml (created)
  flutterdemo/.idea/libraries/KotlinJavaRuntime.xml (created)
  flutterdemo/.idea/modules.xml (created)
  flutterdemo/.idea/workspace.xml (created)
Running "flutter pub get" in flutterdemo...                         3.0s
Wrote 72 files.

All done!

[✓] Flutter: is fully installed. (Channel stable, v1.17.3, on Mac OS X 10.15.4 19E287, locale zh-Hans-CN)
[✓] Android toolchain - develop for Android devices: is fully installed. (Android SDK version 29.0.3)
[✓] Xcode - develop for iOS and macOS: is fully installed. (Xcode 11.4.1)
[✓] Android Studio: is fully installed. (version 3.2)
[!] Android Studio: is partially installed; more components are available. (version 3.0)
[!] Android Studio: is partially installed; more components are available. (version 3.6)
[!] IntelliJ IDEA Community Edition: is partially installed; more components are available. (version 2019.3.1)
[✓] VS Code: is fully installed. (version 1.45.1)
[✓] Connected device: is fully installed. (1 available)

Run "flutter doctor" for information about installing additional components.

In order to run your application, type:

  $ cd flutterdemo
  $ flutter run

Your application code is in flutterdemo/lib/main.dart.
```

随后连接上模拟器，使用`flutter run`运行项目

``` bash
$ flutter run
Launching lib/main.dart on iPhone SE (2nd generation) in debug mode...
Running Xcode build...                                                  
                                                   
 └─Compiling, linking and signing...                         5.8s
Xcode build done.                                           17.3s
Syncing files to device iPhone SE (2nd generation)...              135ms

Flutter run key commands.
r Hot reload. 🔥🔥🔥
R Hot restart.
h Repeat this help message.
d Detach (terminate "flutter run" but leave application running).
c Clear the screen
q Quit (terminate the application on the device).
An Observatory debugger and profiler on iPhone SE (2nd generation) is available at:
http://127.0.0.1:50997/0_E-14-WAWg=/
```

运行就完成啦！

# 参考资料

https://book.flutterchina.club/chapter1/install_flutter.html

https://blog.csdn.net/MugWorld/article/details/100033262

https://github.com/flutter/flutter/issues/57744