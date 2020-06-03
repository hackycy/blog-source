---
title: 解决Mac通过Brew安装Dart报错：Failed to download resource dart
date: 2020-06-03 12:12:35
tags:
    - Dart
categories:
    - Flutter
---

在学习Flutter的第一步从安装就开始踩坑了。安装Dart硬是安装不上。科学上网也有就是安不上。找了些资料，记录了一下解决方案。

<!-- more -->

# 安装

首先是dart官网推荐使用brew命令安装dart，如下图：

![](https://user-images.githubusercontent.com/26972260/83595208-e6d95600-a593-11ea-86d0-36e06d1a7c49.png)

但是出现了以下的错误：

![](https://user-images.githubusercontent.com/26972260/83594943-2a7f9000-a593-11ea-9fd4-99d845819711.png)

详细报错如下：

``` bash
$ brew install dart
Updating Homebrew...
==> Installing dart from dart-lang/dart
==> Downloading https://storage.googleapis.com/dart-archive/channels/stable/release/2.8.3/sdk/dartsdk-macos-x64-release.zip



-=O=-               #    #     #     #                                        
curl: (7) Failed to connect to storage.googleapis.com port 443: Operation timed out
Error: An exception occurred within a child process:
  DownloadError: Failed to download resource "dart"
Download failed: https://storage.googleapis.com/dart-archive/channels/stable/release/2.8.3/sdk/dartsdk-macos-x64-release.zip
```

# 解决

报错中有回显资源的地址链接，这时候我们可以通过自己浏览器或者迅雷去下载到本地。

然后使用终端查看Homebrew的缓存地址：

``` bash
$ brew --cache
/Users/zjyzy/Library/Caches/Homebrew
```

最后将下载下来的文件 拷贝到 上面缓存地址：

``` bash
$ cp ~/Downloads/dartsdk-macos-x64-release.zip /Users/zjyzy/Library/Caches/Homebrew
```

接着再执行命令就可以正常安装成功

``` bash
$ brew install dart
```

如果没有意外，那么就可以直接解决问题了。但是不幸，我还是不能解决问题。继续查找了一些资料：

安装时给命令加个 `-v` 打印命令的详细日志看看:

``` bash
$ brew install dart -v
==> Installing dart from dart-lang/dart
/usr/bin/sandbox-exec -f /private/tmp/homebrew20200603-12459-1ulm7yo.sb nice ruby -W0 -I $LOAD_PATH -- /usr/local/Homebrew/Library/Homebrew/build.rb /usr/local/Homebrew/Library/Taps/dart-lang/homebrew-dart/dart.rb --verbose
==> Downloading https://storage.googleapis.com/dart-archive/channels/stable/release/2.8.3/sdk/dartsdk-macos-x64-release.zip
/usr/bin/curl -q --globoff --show-error --user-agent Homebrew/2.2.2\ \(Macintosh\;\ Intel\ Mac\ OS\ X\ 10.15.4\)\ curl/7.64.1 --fail --location --remote-time --continue-at 0 --output /Users/zjyzy/Library/Caches/Homebrew/downloads/a21b0f967308afab39b415842adf710f903b479ccb472feb7f13960160733911--dartsdk-macos-x64-release.zip.incomplete https://storage.googleapis.com/dart-archive/channels/stable/release/2.8.3/sdk/dartsdk-macos-x64-release.zip
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:--  0:01:15 --:--:--     0
curl: (7) Failed to connect to storage.googleapis.com port 443: Operation timed out
Error: An exception occurred within a child process:
  DownloadError: Failed to download resource "dart"
Download failed: https://storage.googleapis.com/dart-archive/channels/stable/release/2.8.3/sdk/dartsdk-macos-x64-release.zip
```

**注意看这条信息**

``` bash
/usr/bin/curl -q --globoff --show-error --user-agent Homebrew/2.2.2\ \(Macintosh\;\ Intel\ Mac\ OS\ X\ 10.15.4\)\ curl/7.64.1 --fail --location --remote-time --continue-at 0 --output /Users/zjyzy/Library/Caches/Homebrew/downloads/a21b0f967308afab39b415842adf710f903b479ccb472feb7f13960160733911--dartsdk-macos-x64-release.zip.incomplete https://storage.googleapis.com/dart-archive/channels/stable/release/2.8.3/sdk/dartsdk-macos-x64-release.zip
```

我们看到 Homebrew 下载 dart 的缓存地址为:`/Users/zjyzy/Library/Caches/Homebrew/downloads/a21b0f967308afab39b415842adf710f903b479ccb472feb7f13960160733911--dartsdk-macos-x64-release.zip.incomplete`

`XXX.incomplete` 表示下载未完成，但这是 Homebrew 期望的下载文件路径。那么我们将从浏览器下载好的包放到该目录下，并去除`.incomplete`。

``` bash
$ cp ~/Downloads/dartsdk-macos-x64-release.zip /Users/zjyzy/Library/Caches/Homebrew/downloads/a21b0f967308afab39b415842adf710f903b479ccb472feb7f13960160733911--dartsdk-macos-x64-release.zip
```

**先去下载文件路径删除掉未下载好的包**

``` bash
$ cd /Users/zjyzy/Library/Caches/Homebrew/downloads
$ rm -rf a21b0f967308afab39b415842adf710f903b479ccb472feb7f13960160733911--dartsdk-macos-x64-release.zip.incomplete
```

此时在安装查看

``` bash
$ brew install dart -v
Updating Homebrew...
==> Installing dart from dart-lang/dart
/usr/bin/sandbox-exec -f /private/tmp/homebrew20200603-14161-iaxt0b.sb nice ruby -W0 -I $LOAD_PATH -- /usr/local/Homebrew/Library/Homebrew/build.rb /usr/local/Homebrew/Library/Taps/dart-lang/homebrew-dart/dart.rb --verbose
==> Downloading https://storage.googleapis.com/dart-archive/channels/stable/release/2.8.3/sdk/dartsdk-macos-x64-release.zip
Already downloaded: /Users/zjyzy/Library/Caches/Homebrew/downloads/a21b0f967308afab39b415842adf710f903b479ccb472feb7f13960160733911--dartsdk-macos-x64-release.zip
==> Verifying a21b0f967308afab39b415842adf710f903b479ccb472feb7f13960160733911--dartsdk-macos-x64-release.zip checksum
unzip -o /Users/zjyzy/Library/Caches/Homebrew/downloads/a21b0f967308afab39b415842adf710f903b479ccb472feb7f13960160733911--dartsdk-macos-x64-release.zip -d /private/tmp/d20200603-14162-sr2oas
cp -pR /private/tmp/d20200603-14162-sr2oas/dart-sdk/. /private/tmp/dart-20200603-14162-bg4o22/dart-sdk
chmod -Rf +w /private/tmp/d20200603-14162-sr2oas
==> Cleaning
==> Finishing up
ln -s ../Cellar/dart/2.8.3/bin/dart dart
ln -s ../Cellar/dart/2.8.3/bin/dart2js dart2js
ln -s ../Cellar/dart/2.8.3/bin/dart2native dart2native
ln -s ../Cellar/dart/2.8.3/bin/dartanalyzer dartanalyzer
ln -s ../Cellar/dart/2.8.3/bin/dartaotruntime dartaotruntime
ln -s ../Cellar/dart/2.8.3/bin/dartdevc dartdevc
ln -s ../Cellar/dart/2.8.3/bin/dartdoc dartdoc
ln -s ../Cellar/dart/2.8.3/bin/dartfmt dartfmt
ln -s ../Cellar/dart/2.8.3/bin/pub pub
/usr/bin/sandbox-exec -f /private/tmp/homebrew20200603-14268-12qkciz.sb nice ruby -W0 -I $LOAD_PATH -- /usr/local/Homebrew/Library/Homebrew/postinstall.rb /usr/local/Homebrew/Library/Taps/dart-lang/homebrew-dart/dart.rb -v
==> Caveats
Please note the path to the Dart SDK:
  /usr/local/opt/dart/libexec
==> Summary
🍺  /usr/local/Cellar/dart/2.8.3: 502 files, 486MB, built in 7 seconds
==> `brew cleanup` has not been run in 30 days, running now...
Removing: /Users/zjyzy/Library/Caches/Homebrew/openssl@1.1--1.1.1d.mojave.bottle.tar.gz... (5.2MB)
Removing: /Users/zjyzy/Library/Caches/Homebrew/python--3.7.4_1.mojave.bottle.tar.gz... (14.6MB)
Removing: /Users/zjyzy/Library/Caches/Homebrew/readline--8.0.1.mojave.bottle.tar.gz... (517.9KB)
Removing: /Users/zjyzy/Library/Caches/Homebrew/sqlite--3.29.0.mojave.bottle.tar.gz... (1.9MB)
Removing: /Users/zjyzy/Library/Caches/Homebrew/watchman--4.9.0_3.mojave.bottle.tar.gz... (536.8KB)
Removing: /Users/zjyzy/Library/Caches/Homebrew/Cask/motrix--1.4.1.dmg... (63MB)
rm /usr/local/share/man/man5/npm-folders.5
rm /usr/local/share/man/man5/npm-global.5
rm /usr/local/share/man/man5/npm-json.5
rm /usr/local/share/man/man5/npm-package-locks.5
rm /usr/local/share/man/man5/npm-shrinkwrap.json.5
rm /usr/local/share/man/man5/package-lock.json.5
rm /usr/local/share/man/man5/package.json.5
rm /usr/local/share/man/man7/npm-coding-style.7
rm /usr/local/share/man/man7/npm-config.7
rm /usr/local/share/man/man7/npm-developers.7
rm /usr/local/share/man/man7/npm-disputes.7
rm /usr/local/share/man/man7/npm-index.7
rm /usr/local/share/man/man7/npm-orgs.7
rm /usr/local/share/man/man7/npm-registry.7
rm /usr/local/share/man/man7/npm-scope.7
rm /usr/local/share/man/man7/npm-scripts.7
rm /usr/local/share/man/man7/removing-npm.7
rmdir /usr/local/lib/node_modules/@vue/cli/node_modules/@apollo/protobufjs/cli/node_modules
Pruned 17 symbolic links and 1 directories from /usr/local
```

命令验证dart安装

``` bash
$ brew info dart
dart-lang/dart/dart: stable 2.8.3, devel 2.9.0-13.0.dev
The Dart SDK
https://dart.dev
Conflicts with:
  dart-beta (because dart-beta ships the same binaries)
/usr/local/Cellar/dart/2.8.3 (502 files, 486MB) *
  Built from source on 2020-06-03 at 13:04:47
From: https://github.com/dart-lang/homebrew-dart/blob/master/dart.rb
==> Options
--devel
	Install development version 2.9.0-13.0.dev
==> Caveats
Please note the path to the Dart SDK:
  /usr/local/opt/dart/libexec
```

至此，dartSDK已经安装成功。

# 参考资料

https://shockerli.net/post/homebrew-install-download-error/

https://www.cnblogs.com/lmyupupblogs/p/12785753.html

