---
title: IOS之NSUserDefaults
date: 2019-08-12 15:27:44
keywords:
    - Ios
    - NSUserDefaults
tags:
    - Ios
categories:
    - Ios
---

# 概览

`NSUserDefaults`类提供了一个与默认系统交互的编程接口。默认系统允许应用程序自定义其行为以匹配用户的首选项。例如，您可以允许用户指定其首选的测量单位或媒体播放速度。应用程序通过为用户默认数据库中的一组参数指定值来存储这些首选项。这些参数被称为默认值，因为它们通常用于确定应用程序启动时的默认状态或默认情况下的行为方式。在运行时，使用`NSUserDefaults`对象从用户的默认数据库中读取应用程序使用的默认值。nsUserDefaults缓存信息，以避免每次需要默认值时都必须打开用户的默认数据库。当您设置默认值时，它会在您的进程内同步更改，并异步更改为持久存储和其他进程。

<!-- more -->

![](overview.png)

如果是一些大型数据，毫无疑问是用一些SQLite或者FMDB等来存储大量数据。但是又一些轻量级别的数据，例如用户的信息、用户的偏好设置，使用该类是最为简单方便的。它通过键值对的方式来来存储一系列的偏好设置来把对象存储/读取相应的`plist`文件中。那么既然是一个`plist`，则NSUserDefaults能够存储的数据类型有[`NSData`](https://developer.apple.com/documentation/foundation/nsdata?language=objc), [`NSString`](https://developer.apple.com/documentation/foundation/nsstring?language=objc), [`NSNumber`](https://developer.apple.com/documentation/foundation/nsnumber?language=objc), [`NSDate`](https://developer.apple.com/documentation/foundation/nsdate?language=objc), [`NSArray`](https://developer.apple.com/documentation/foundation/nsarray?language=objc), 和[`NSDictionary`](https://developer.apple.com/documentation/foundation/nsdictionary?language=objc)。如果需要存储某些不支持的类型，可以先将其归档为NSData类型再进行存储。

**NSUserDefaults**是一个单例类，在整个程序运行过程中只有一个实例对象，同时也是线程安全的。获取方式：

``` objc
NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
```

# 存储数据

存储一些默认支持的数据类型：

``` objc
//存储数据
[defaults setObject:@"hackycy" forKey:@"username"];
[defaults setInteger:22 forKey:@"age"];
[defaults setBool:YES forKey:@"isBoy"];
[defaults setFloat:1.73 forKey:@"mmForHeight"];
[defaults setURL:[NSURL URLWithString:@"https://hackycy.github.io"] forKey:@"blog"];
[defaults setDouble:7500 forKey:@"wage"];
```

**更多方法**

``` objc
//Setting Default Values
- setObject:forKey:
//Sets the value of the specified default key.

- setFloat:forKey:
//Sets the value of the specified default key to the specified float value.

- setDouble:forKey:
//Sets the value of the specified default key to the double value.

- setInteger:forKey:
//Sets the value of the specified default key to the specified integer value.

- setBool:forKey:
//Sets the value of the specified default key to the specified Boolean value.

- setURL:forKey:
//Sets the value of the specified default key to the specified URL.
```

# 读取数据

从上面存储的数据中读取并打印：

``` objc
//获取数据   
NSString *username = [defaults stringForKey:@"username"];
NSInteger age = [defaults integerForKey:@"age"];
BOOL isBoy = [defaults boolForKey:@"isBoy"];
float mmForHeight = [defaults floatForKey:@"mmForHeight"];
NSURL *blog = [defaults URLForKey:@"blog"];
double wage = [defaults doubleForKey:@"wage"];
NSData *imageData = [defaults objectForKey:@"avatar"];
self.imageView.image = [UIImage imageWithData:imageData];
NSLog(@"username : %@, age : %lu, isBoy : %@, mmForHeight : %f, blog : %@, wage : %f", username, (unsigned long)age, isBoy ? @"boy" : @"girl", mmForHeight, blog, wage);
```

**更多方法**

``` objc
//Getting Default Values
- objectForKey:
//Returns the object associated with the specified key.

- URLForKey:
//Returns the URL associated with the specified key.

- arrayForKey:
//Returns the array associated with the specified key.

- dictionaryForKey:
//Returns the dictionary object associated with the specified key.

- stringForKey:
//Returns the string associated with the specified key.

- stringArrayForKey:
//Returns the array of strings associated with the specified key.

- dataForKey:
//Returns the data object associated with the specified key.

- boolForKey:
//Returns the Boolean value associated with the specified key.

- integerForKey:
//Returns the integer value associated with the specified key.

- floatForKey:
//Returns the float value associated with the specified key.

- doubleForKey:
//Returns the double value associated with the specified key.

- dictionaryRepresentation
//Returns a dictionary that contains a union of all key-value pairs in the domains in the search list.
```

# registerDefaults

先让我们来看一种情况：

``` objc
BOOL isNeedShowGuide = [defaults boolForKey:@"isNeedShowGuide"];
NSLog(@"%@", isNeedShowGuide ? @"YES" : @"NO");
```

LOG：

``` objc
2019-08-13 11:54:29.402946+0800 nsuserdefaultdemo[4616:174076] NO
```

当我们APP首次启动时需要进入一个引导页，通过该`isNeedShowGuide`来判断是否进入引导页，而首次进入是需要YES的情况下进入引导页，后续将值设置为NO。但是通过打印该值时获取到的不是我们想要的值，因为该值如果设置过才会运行正确。但是没有设置过该值也没有该默认值则默认返回的是NO。

那么这里就需要用到`registerDefaults`方法了：

``` objc
[[NSUserDefaults standardUserDefaults] registerDefaults:@{
    @"isNeedShowGuide" : @YES
}];
```

我们在获取`isNeedShowGuide`之前先调用了`registerDefaults`方法设置了它的默认值，后面获取的时候即使没有再设置值也会获取到默认设置的值，就不会出现上述获取默认值尴尬的情况了。

当然我们也可以先默认保存一个plist文件在项目目录中来设置默认值来方便读取，来减少代码量：

``` objc
NSURL *plistUrl = [[NSBundle mainBundle] URLForResource:@"DefaultPreferences" withExtension:@"plist"];
NSDictionary *defaultPreferences = [NSDictionary dictionaryWithContentsOfURL:plistUrl];
[[NSUserDefaults standardUserDefaults] registerDefaults:defaultPreferences];
```

**注意：**`registerDefaults` 设置的默认值是不会持久化存储的，也就是说我们每次启动 APP 的时候，都需要这样设置一遍。所以`application:didFinishLaunchingWithOptions`是最合适的地方。

# NSUserDefaults域

NSUserDefaults 还有一个 Domain 的概念，当我们调用 `[NSUserDefaults standardUserDefaults]`方法时，就会初始化 `NSUserDefaults`， 并且它默认会包含 5 个 Domain， 分别是：

- **NSArgumentDomain**：最高优先权
- **Application**：它存储着你app通过`NSUserDefaults set...forKey`添加的设置
- **NSGlobalDomain**：存储着系统的设置
- **Languages**：包括地区、日期等
- **NSRegistrationDomain**：仅有较低的优先权，只有在应用域没有找到值时才从注册域去寻找。

来解释一下，比如调用了下面的一个方法：

``` objc
[[NSUserDefaults standardUserDefaults] setBool:NO forKey:@"isNeedShowGuide"];
```

这句话会将设置的key和值存储在了`Application`域中，但 `NSUserDefaults` 还包括了其他 4 个域，如果我们每次调用读取数据的方法的时候，例如：

``` objc
[[NSUserDefaults standardUserDefaults] boolForKey:@"isNeedShowGuide"]
```

那么其实底层的就会进行一次搜索，搜索顺序为：

`NSArgumentDomain` -> `Application` -> `NSGlobalDomain` -> `Languages` -> `NSRegistrationDomain`

而之前举的例子来说，我们并没有通过`setBool:forkey:`方法来设置该值，所以`Application`域中并没有该值，但是我们使用了`registerDefaults`将它设置到了`NSRegistrationDomain`域中。

所以按照` NSUserDefaults` 的默认搜索顺序，就会找到最后 `NSRegistrationDomain` 域中的那个 `isNeedShowGuide`， 也就是我们所谓的默认值 YES 了。

# 存储路径

NSUserDefaults 数据存放在沙盒 `Library/Preferences/` 目录下，一个以你包名命名的`.plist`文件。

通过代码获取路径后：

```objc
NSString *path = NSHomeDirectory();
NSLog(@"path : %@", path);
```

将路径复制到Finder中，前往：

![](cunchu.png)

# 关于synchronize方法

该方法是为了强制存储，但其实是没有必要的，该方法系统会默认调用。

> Waits for any pending asynchronous updates to the defaults database and returns; this method is unnecessary and shouldn't be used.
>
> 等待默认数据库的任何挂起的异步更新并返回; 此方法是不必要的，不应使用。

# 参考链接

https://developer.apple.com/documentation/foundation/nsuserdefaults?language=objc

http://swiftcafe.io/post/nsuserdefaults