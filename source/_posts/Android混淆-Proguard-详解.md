---
title: Android混淆(Proguard)详解
date: 2019-04-12 17:11:12
keywords:
    - Android
    - 混淆
tags:
    - Android
categories:
    - Android
---

# 混淆的作用及好处

混淆属于整个应用程序开发生命周期偏后期阶段的技术了，所以要考虑应用的安全性及性能的问题，混淆就是为了这种需求产生的一种技术，简单说，混淆就是将关键字和关键类名，修改为无意义的字符以起到迷惑试图反编译去查看源码的人。在一定程度上能过滤掉一些入门反编译者，混淆是保障Android程序源码安全的第一道门槛，
以上谈了下混淆的作用，而混淆的好处除了能保证源码安全性之外就大概是通过修改关键字为无意义字符串，或者剔除某些辅助类，比如Log，从而减少文件大小。

<!-- more -->

# 混淆的原理

[proguard官网](https://www.guardsquare.com/en/products/proguard)

Java 是一种跨平台的、解释型语言，Java 源代码编译成中间”字节码”存储于 class 文件中。由于跨平台的需要，Java   字节码中包括了很多源代码信息，如变量名、方法名，并且通过这些名称来访问变量和方法，这些符号带有许多语义信息，很容易被反编译成 Java 源代码。为了防止这种现象，我们可以使用 Java 混淆器对 Java 字节码进行混淆。
混淆就是对发布出去的程序进行重新组织和处理，使得处理后的代码与处理前代码完成相同的功能，而混淆后的代码很难被反编译，即使反编译成功也很难得出程序的真正语义。被混淆过的程序代码，仍然遵照原来的档案格式和指令集，执行结果也与混淆前一样，只是混淆器将代码中的所有变量、函数、类的名称变为简短的英文字母代号，在缺乏相应的函数名和程序注释的况下，即使被反编译，也将难以阅读。同时混淆是不可逆的，在混淆的过程中一些不影响正常运行的信息将永久丢失，这些信息的丢失使程序变得更加难以理解。
混淆器的作用不仅仅是保护代码，它也有精简编译后程序大小的作用。由于以上介绍的缩短变量和函数名以及丢失部分信息的原因， 编译后 jar 文件体积大约能减少25%，这对当前费用较贵的无线网络传输是有一定意义的。

# 混淆的具体使用

模块(Module)下的build.gradle的配置

``` gradle
android{
  buildTypes{
    release { 
       // 是否进行混淆  
       minifyEnabled false  
       // 混淆文件的位置，其中'proguard-android.txt'为sdk默认的混淆配置，
       //'proguard-rules.pro' 是该模块下的混淆配置
       proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'  
    } 
  }
}
```

以上是proguard在模块下build.gradle文件中的配置信息，其中`proguard-android.txt`为sdk默认的混淆配置，`proguard-rules.pro`是在默认配置的基础上针对本模块做出的针对性混淆处理。

- proguard-android.txt 这个文件是系统默认混淆文件一般不需要做修改
- 在 debug 版下也可以开启混淆做为测试
- Gradle 2.2 之后，defaultProguardFile 没有使用 SDK 目录下的 proguard-android.txt，而是使用了 gradle 自带的 proguard-android.txt，不同的 gradle 版本带有不同的默认混淆文件，比如在项目根目录的 build/intermediates/proguard-files/proguard-android.txt-2.3.3，即为 gradle 自带的混淆文件。在 proguard-android.txt-2.3.3 文件中也写有说明，Gradle 2.2 之后自带混淆文件

>注：proguard-android.txt的位置位于android-sdk/tools/proguard/proguard-android.txt

# 混淆规则

这个语法的作用是定义出 **不需要**混淆的源代码，那么编译时会自动将未定义的部分全都混淆。而如下是不需要混淆的

- Android 四大组件
- native方法
- Java 反射用到的类
- 自定义控件
- 枚举类
- JavaBean
- Parcelable、Serializable 序列化类
- WebView 与 JS 交互所用到的类和方法

# 混淆步骤

![](proguard.png)

**proguard分为4个步骤：**

1. 压缩（shrink）
   移除未使用的类、方法、字段等；
2. 优化（optimize）
   优化字节码、简化代码等操作；
3. 混淆（obfuscate）
   使用简短的、无意义的名称重全名类名、方法名、字段等；
4. 预校验（preverify）
   为class添加预校验信息。


# 混淆基本指令

``` proguard
-dontshrink
# 声明不进行压缩操作，默认情况下，除了-keep配置（下详）的类及其直接或间接引用到的类，都会被移除。

#---------------------------------------------- shrink

-dontoptimize
# 不对class进行优化，默认开启优化。
# 注意：由于优化会进行类合并、内联等多种优化，-applymapping可能无法完全应用，需使用热修复的应用，建议使用此# 配置关闭优化。

-optimizationpasses n
# 执行优化的次数，默认1次，多次能达到更好的优化效果。

-optimizations optimization_filter
优化配置，可进行字段优化、内联、类合并、代码简化、算法指令精简等操作。

#只进行 移除未使用的局部变量、算法指令精简
-optimizations code/removal/variable,code/simplification/arithmetic

#进行除 算法指令精简、字段、类合并外的所有优化
-optimizations !code/simplification/arithmetic,!field/*,!class/merging/*

#---------------------------------------------- optimize

-dontobfuscate
# 不进行混淆，默认开启混淆。除-keep指定的类及成员外，都会被替换成简短、随机的名称，以达到混淆的目的。

-applymapping filename
# 根据指定的mapping映射文件进行混淆。

-obfuscationdictionary filename
# 指定字段、方法名的混淆字典，默认情况下使用abc等字母组合，比如根据自己的喜好指定中文、特殊字符进行混淆命名。

-classobfuscationdictionary filename
# 指定类名混淆字典。

-packageobfuscationdictionary filename
# 指定包名混淆字典。

-useuniqueclassmembernames
# 指定相同的混淆名称对应不同类的相同成员，不同的混淆名称对应不同的类成员。在没有指定这个选项时，不同类的不同方法都可能映射到a,b,c。

# 有一种情况，比如两个不同的接口，拥有相同的方法签名，在没有指定这个选项时，这两个接口的方法可能混淆成不同的名称。但如果新增一个类同时实现这两个接口，并且利用-applymapping指定之前的mapping映射文件时，这两个接口的方法必须混淆成相同的名称，这时就和之前的mapping冲突了。

# 在某此热修复场景下需要指定此选项。

-dontusemixedcaseclassnames
# 指定不使用大小写混用的类名，默认情况下混淆后的类名可能同时包含大写小字母。这在某些对大小写不敏感的系统（如windowns）上解压时，可能存在文件被相互覆盖的情况。

-keeppackagenames [package_filter]
# 指定不混淆指定的包名，多个包名可以用逗号分隔，可以使用? * **通配符，并且可以使用否定符（!）。

-keepattributes [attribute_filter]
# 指定保留属性，多个属性可以用多个-keepattributes配置，也可以用逗号分隔，可以使用? * **通配符，并且可以使用否定符（!）。
# 比如，在混淆ibrary库时，应该至少keep Exceptions, InnerClasses, Signature；如果在追踪代码，还需要keep符号表；使用到注解时也需要keep。
-keepattributes Exceptions,InnerClasses,Signature
-keepattributes SourceFile,LineNumberTable
-keepattributes *Annotation*

-keepparameternames
# 指定keep已经被keep的方法的参数类型和参数名称，在混淆library库时非常有用，可供IDE帮助用户进行信息提示和代码自动填充。

#---------------------------------------------- obfuscate

-dontpreverify
# 指定不对class进行预校验，默认情况下，在编译版本为micro或者1.6或更高版本时是开启的。但编译成Android版本时，预校验是不必须的，配置这个选项可以节省一点编译时间。（Android会把class编译成dex，并对dex文件进行校验，对class进行预校验是多余的。）

#---------------------------------------------- preverify
```

## keep配置

``` proguard
# -keep [,modifier,...] class_specification
# 指定类及类成员作为代码入口，保护其不被proguard，如：
-keep class com.rush.Test
-keep interface com.rush.InterfaceTest
-keep class com.rush.** {
    <init>;
    public <fields>;
    public <methods>;
    public *** get*();
    void set*(***);
}
```

- class表示keep类或接口
- interface仅表示keep接口

**类名 通配符如下:**

| 通配符 | 含义                                                         |
| :----- | :----------------------------------------------------------- |
| ?      | 匹配单个字符，包名分隔符（.）除外                            |
| *      | 匹配除（.）外的任意字符                                      |
| **     | 匹配任意字符（包含.），如com.rush.**匹配com.rush包下的所有类及其所有子包的类。 |

**字段和方法 通配符如下：**

| 通配符      | 含义                              |
| :---------- | :-------------------------------- |
| `<init>`    | 匹配所有构造方法                  |
| `<fields>`  | 匹配所有字段                      |
| `<methods>` | 匹配所有方法                      |
| ?           | 匹配单个字符，包名分隔符（.）除外 |
| *           | 匹配除（.）外的任意字符           |

**类型 通配符如下：**

| 通配符 | 含义                                                   |
| :----- | :----------------------------------------------------- |
| %      | 匹配原始类型，如int, boolean等                         |
| ?      | 匹配任意单个字符                                       |
| *      | 匹配除包名分隔符（.）外的任意字符                      |
| **     | 匹配任意字符，包括包名分隔符（.）                      |
| ***    | 匹配任意类型（原始类型、非原始类型、数组或非数组类型） |
| …      | 匹配任意参数个数，任意参数类型                         |

**其中类配置完整定义如下，其中[]表示可选：**

``` proguard
[@annotationtype] [[!]public|final|abstract|@ ...] [!]interface|class|enum classname
    [extends|implements [@annotationtype] classname]
[{
    [@annotationtype] [[!]public|private|protected|static|volatile|transient ...] <fields> |
                                                                      (fieldtype fieldname);
    [@annotationtype] [[!]public|private|protected|static|synchronized|native|abstract|strictfp ...] <methods> |
                                                                                           <init>(argumenttype,...) |
                                                                                           classname(argumenttype,...) |
                                                                                           (returntype methodname(argumenttype,...));
    [@annotationtype] [[!]public|private|protected|static ... ] *;
    ...
}]
```

| 保留                           | 防止被移除或重命名      | 防止被重命名（未使用的会被移除） |
| :----------------------------- | :---------------------- | :------------------------------- |
| 类和类成员                     | -keep                   | -keepnames                       |
| 仅类成员                       | -keepclassmembers       | -keepclassmembernames            |
| 如类含有某成员，保留类及其成员 | -keepclasseswithmembers | -keepclasseswithmembernames      |

# 更多详细指令

``` proguard
# 代码混淆压缩比，在 0~7 之间
-optimizationpasses 5

# 不提示警告
-dontwarn

# 混合时不使用大小写混合，混合后的类名为小写
-dontusemixedcaseclassnames

# 指定不忽略非公共库的类和类成员
-dontskipnonpubliclibraryclasses
-dontskipnonpubliclibraryclassmembers

# 这句话能够使我们的项目混淆后产生映射文件
# 包含有类名->混淆后类名的映射关系
-verbose

# 不做预校验，preverify是proguard的四个步骤之一，Android不需要preverify，去掉这一步能够加快混淆速度
-dontpreverify

# 保留Annotation不混淆
-keepattributes *Annotation*,InnerClasses

# 避免混淆泛型
-keepattributes Signature

# 抛出异常时保留代码行号
-keepattributes SourceFile,LineNumberTable

# 指定混淆是采用的算法，后面的参数是一个过滤器
# 这个过滤器是 Google 推荐的算法，一般不做修改
-optimizations !code/simplification/arithmetic,!code/simplification/cast,!field/*,!class/merging/*

# 是否允许改变作用域的，可以提高优化效果
# 但是，如果你的代码是一个库的话，最好不要配置这个选项，因为它可能会导致一些 private 变量被改成 public，谨慎使用
#-allowaccessmodification

# 指定一些接口可能会被合并，即使一些子类没有同时实现两个接口的方法。这种情况在java源码中是不允许存在的，但是在java字节码中是允许存在的。
# 它的作用是通过合并接口减少类的数量，从而达到减少输出文件体积的效果。仅在 optimize 阶段有效。
# 如果在开启后没有任何影响可以使用，这项配置对于一些虚拟机的65535方法数限制是有一定效果的，谨慎使用
#-mergeinterfacesaggressively

# 输出所有找不到引用和一些其它错误的警告，但是继续执行处理过程。不处理警告有些危险，所以在清楚配置的具体作用的时候再使用
-ignorewarnings
-include {filename}     #从给定的文件中读取配置参数
-basedirectory {directoryname}    #指定基础目录为以后相对的档案名称
-injars {class_path}    #指定要处理的应用程序jar,war,ear和目录
-outjars {class_path}     #指定处理完后要输出的jar,war,ear和目录的名称
-libraryjars {classpath}     #指定要处理的应用程序jar,war,ear和目录所需要的程序库文件
-dontskipnonpubliclibraryclasses     #指定不去忽略非公共的库类。
-dontskipnonpubliclibraryclassmembers     #指定不去忽略包可见的库类的成员。

 #保留选项
-keep {Modifier} {class_specification}     #保护指定的类文件和类的成员
-keepclassmembers {modifier} {class_specification}     #保护指定类的成员，如果此类受到保护他们会保护的更好
-keepclasseswithmembers {class_specification}     #保护指定的类和类的成员，但条件是所有指定的类和类成员是要存在。
-keepnames {class_specification}     #保护指定的类和类的成员的名称（如果他们不会压缩步骤中删除）
-keepclassmembernames {class_specification}     #保护指定的类的成员的名称（如果他们不会压缩步骤中删除）
-keepclasseswithmembernames {class_specification}     #保护指定的类和类的成员的名称，如果所有指定的类成员出席（在压缩步骤之后）
-printseeds {filename}     #列出类和类的成员-keep选项的清单，标准输出到给定的文件

 #压缩
-dontshrink     #不压缩输入的类文件
-printusage {filename}
-whyareyoukeeping {class_specification}

 #优化
-dontoptimize     #不优化输入的类文件
-assumenosideeffects {class_specification}     #优化时假设指定的方法，没有任何副作用
-allowaccessmodification     #优化时允许访问并修改有修饰符的类和类的成员

 #混淆
-dontobfuscate     #不混淆输入的类文件
-printmapping {filename}
-applymapping {filename}     #重用映射增加混淆
-obfuscationdictionary {filename}     #使用给定文件中的关键字作为要混淆方法的名称
-overloadaggressively     #混淆时应用侵入式重载
-useuniqueclassmembernames     #确定统一的混淆类的成员名称来增加混淆
-flattenpackagehierarchy {package_name}     #重新包装所有重命名的包并放在给定的单一包中
-repackageclass {package_name}     #重新包装所有重命名的类文件中放在给定的单一包中
-dontusemixedcaseclassnames     #混淆时不会产生形形色色的类名
-keepattributes {attribute_name,...}     #保护给定的可选属性，例如LineNumberTable, LocalVariableTable, SourceFile, Deprecated, Synthetic, Signature, and

 #InnerClasses.
-renamesourcefileattribute {string}     #设置源文件中给定的字符串常量

```

# 混淆日志

``` proguard
# APK 包内所有 class 的内部结构
-dump proguard/class_files.txt
# 未混淆的类和成员
-printseeds proguard/seeds.txt
# 列出从 APK 中删除的代码
-printusage proguard/unused.txt
# 混淆前后的映射，这个文件在追踪异常的时候是有用的
-printmapping proguard/mapping.txt
```

# 其他自定义混淆规则

``` proguard
# JavaBean 实体类不能混淆，一般会将实体类统一放到一个包下，you.package.path 请改成你自己的项目路径
-keep public class com.frame.mvp.entity.** {
    *;
}

# 网页中的 JavaScript 进行交互，you.package.path 请改成你自己的项目路径
#-keepclassmembers class you.package.path.JSInterface {
#    <methods>;
#}

# 需要通过反射来调用的类，没有可忽略，you.package.path 请改成你自己的项目路径
#-keep class you.package.path.** { *; }
```

# 一些不是很常用但比较实用的混淆命令

``` proguard
# 所有重新命名的包都重新打包，并把所有的类移动到所给定的包下面。如果没有指定 packagename，那么所有的类都会被移动到根目录下
# 如果需要从目录中读取资源文件，移动包的位置可能会导致异常，谨慎使用
# you.package.path 请改成你自己的项目路径
-flatternpackagehierarchy

# 所有重新命名过的类都重新打包，并把他们移动到指定的packagename目录下。如果没有指定 packagename，同样把他们放到根目录下面。
# 这项配置会覆盖-flatternpackagehierarchy的配置。它可以代码体积更小，并且更加难以理解。
# you.package.path 请改成你自己的项目路径
-repackageclasses you.package.path

# 指定一个文本文件用来生成混淆后的名字。默认情况下，混淆后的名字一般为 a、b、c 这种。
# 通过使用配置的字典文件，可以使用一些非英文字符做为类名。成员变量名、方法名。字典文件中的空格，标点符号，重复的词，还有以'#'开头的行都会被忽略。
# 需要注意的是添加了字典并不会显著提高混淆的效果，只不过是更不利与人类的阅读。正常的编译器会自动处理他们，并且输出出来的jar包也可以轻易的换个字典再重新混淆一次。
# 最有用的做法一般是选择已经在类文件中存在的字符串做字典，这样可以稍微压缩包的体积。
# 字典文件的格式：一行一个单词，空行忽略，重复忽略
-obfuscationdictionary

# 指定一个混淆类名的字典，字典格式与 -obfuscationdictionary 相同
#-classobfuscationdictionary
# 指定一个混淆包名的字典，字典格式与 -obfuscationdictionary 相同
-packageobfuscationdictionary

# 混淆的时候大量使用重载，多个方法名使用同一个混淆名，但是他们的方法签名不同。这可以使包的体积减小一部分，也可以加大理解的难度。仅在混淆阶段有效。
# 这个参数在 JDK 版本上有一定的限制，可能会导致一些未知的错误，谨慎使用
-overloadaggressively

# 方法同名混淆后亦同名，方法不同名混淆后亦不同名。不使用该选项时，类成员可被映射到相同的名称。因此该选项会增加些许输出文件的大小。
-useuniqueclassmembernames

# 指定在混淆的时候不使用大小写混用的类名。默认情况下，混淆后的类名可能同时包含大写字母和小写字母。
# 这样生成jar包并没有什么问题。只有在大小写不敏感的系统（例如windows）上解压时，才会涉及到这个问题。
# 因为大小写不区分，可能会导致部分文件在解压的时候相互覆盖。如果有在windows系统上解压输出包的需求的话，可以加上这个配置。
-dontusemixedcaseclassnames
```

# Android开发常用不需要混淆指令

``` proguard
# Android 四大组件相关
-keep public class * extends android.app.Activity
-keep public class * extends android.app.Appliction
-keep public class * extends android.app.Service
-keep public class * extends android.content.BroadcastReceiver
-keep public class * extends android.content.ContentProvider
-keep public class * extends android.app.backup.BackupAgentHelper
-keep public class * extends android.preference.Preference
-keep public class * extends android.view.View
-keep public class com.android.vending.licensing.ILicensingService

# Fragment
-keep public class * extends android.support.v4.app.Fragment
-keep public class * extends android.app.Fragment

# 保留support下的所有类及其内部类
-keep class android.support.** { *; }
-keep interface android.support.** { *; }
-dontwarn android.support.**

# 保留 R 下面的资源
-keep class **.R$* {*;}
-keepclassmembers class **.R$* {
    public static <fields>;
}

# 保留本地 native 方法不被混淆
-keepclasseswithmembernames class * {
    native <methods>;
}

# 保留在 Activity 中的方法参数是 view 的方法，
# 这样以来我们在 layout 中写的 onClick 就不会被影响
-keepclassmembers class * extends android.app.Activity{
    public void *(android.view.View);
}

# 保留枚举类不被混淆
-keepclassmembers enum * {
    public static **[] values();
    public static ** valueOf(java.lang.String);
}

# 保留自定义控件（继承自View）不被混淆
-keep public class * extends android.view.View{
    *** get*();
    void set*(***);
    public <init>(android.content.Context);
    public <init>(android.content.Context, android.util.AttributeSet);
    public <init>(android.content.Context, android.util.AttributeSet, int);
}

# 保留 Parcelable 序列化类不被混淆
-keep class * implements android.os.Parcelable {
  public static final android.os.Parcelable$Creator *;
}

# 保留 Serializable 序列化的类不被混淆
-keepclassmembers class * implements java.io.Serializable {
    static final long serialVersionUID;
    private static final java.io.ObjectStreamField[] serialPersistentFields;
    private void writeObject(java.io.ObjectOutputStream);
    private void readObject(java.io.ObjectInputStream);
    java.lang.Object writeReplace();
    java.lang.Object readResolve();
}

# 对于带有回调函数的 onXXEvent 的，不能被混淆
-keepclassmembers class * {
    void *(**On*Event);
}

# WebView，没有使用 WebView 请注释掉
-keepclassmembers class fqcn.of.javascript.interface.for.webview {
   public *;
}
-keepclassmembers class * extends android.webkit.webViewClient {
    public void *(android.webkit.WebView, java.lang.String, android.graphics.Bitmap);
    public boolean *(android.webkit.WebView, java.lang.String);
}
-keepclassmembers class * extends android.webkit.webViewClient {
    public void *(android.webkit.webView, jav.lang.String);
}

# 不混淆使用了 @Keep 注解相关的类
-keep class android.support.annotation.Keep

-keep @android.support.annotation.Keep class * {*;}

-keepclasseswithmembers class * {
    @android.support.annotation.Keep <methods>;
}

-keepclasseswithmembers class * {
    @android.support.annotation.Keep <fields>;
}

-keepclasseswithmembers class * {
    @android.support.annotation.Keep <init>(...);
}

# 删除代码中 Log 相关的代码，如果删除了一些预料之外的代码，很容易就会导致代码崩溃，谨慎使用
-assumenosideeffects class android.util.Log{
   public static boolean isLoggable(java.lang.String,int);
   public static int v(...);
   public static int i(...);
   public static int w(...);
   public static int d(...);
   public static int e(...);
}

# 删除自定义Log工具
-assumenosideeffects class com.example.Log.Logger{
   public static int v(...);
   public static int i(...);
   public static int w(...);
   public static int d(...);
   public static int e(...);
}
```

# proguard配置示例

## Android默认推荐配置

在IDE自动生成的project.properties文件中，有这样一行：

``` properties
#proguard.config=${sdk.dir}/tools/proguard/proguard-android.txt:proguard-project.txt
```

Android Studio默认生成的build.gradle文件有如下配置：

``` groovy
buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
}
```

其中getDefaultProguardFile('proguard-android.txt')获取的也是tools/proguard/proguard-android.txt。

下面看一下这个文件的配置

``` pro
# 不使用大小写混合类名
-dontusemixedcaseclassnames
# 不路过引用库中的非public类
-dontskipnonpubliclibraryclasses
# 输出更多信息
-verbose

# 不进行优化
-dontoptimize
# 不进行预校验
-dontpreverify

# keep注解
-keepattributes *Annotation*
#keep google license服务接口
-keep public class com.google.vending.licensing.ILicensingService
-keep public class com.android.vending.licensing.ILicensingService

# keep native方法及其所属类
-keepclasseswithmembernames class * {
    native <methods>;
}

# keep自定义view的get/set方法
-keepclassmembers public class * extends android.view.View {
   void set*(***);
   *** get*();
}

# keep继续自Activity中所有包含public void *(android.view.View)签名的方法，如onClick
-keepclassmembers class * extends android.app.Activity {
   public void *(android.view.View);
}

# keep枚举中的values和valueOf方法
-keepclassmembers enum * {
    public static **[] values();
    public static ** valueOf(java.lang.String);
}

# keep Parcelable的CREATOR成员
-keepclassmembers class * implements android.os.Parcelable {
  public static final android.os.Parcelable$Creator CREATOR;
}

# keep R文件的静态字段
-keepclassmembers class **.R$* {
    public static <fields>;
}

# 不输出support包中的警告
-dontwarn android.support.**
```

## 典型library库的配置

示例引用自官方文档samples:*https://www.guardsquare.com/en/products/proguard/manual/examples#library*

``` pro
# 这个配置未弄清楚，待测试
-renamesourcefileattribute SourceFile 
-keepattributes Exceptions,InnerClasses,Signature,Deprecated,
                SourceFile,LineNumberTable,*Annotation*,EnclosingMethod 

# keep所有类的protected成员
-keep public class * { 
      public protected *; 
} 

# keep在jdk 1.2中编译器插入的代码
-keepclassmembernames class * { 
    java.lang.Class class$(java.lang.String); 
    java.lang.Class class$(java.lang.String, boolean); 
} 

# keep native方法
-keepclasseswithmembernames,includedescriptorclasses class * { 
    native <methods>; 
} 

# keep枚举中的values和valueOf方法
-keepclassmembers,allowoptimization enum * { 
    public static **[] values(); 
    public static ** valueOf(java.lang.String); 
} 

# keep系列化相关方法
-keepclassmembers class * implements java.io.Serializable { 
    static final long serialVersionUID; 
    private static final java.io.ObjectStreamField[] serialPersistentFields; 
    private void writeObject(java.io.ObjectOutputStream); 
    private void readObject(java.io.ObjectInputStream); 
    java.lang.Object writeReplace(); 
    java.lang.Object readResolve(); 
}
```

## 一个典型Android App的配置

示例引用自官方文档samples:*https://www.guardsquare.com/en/products/proguard/manual/examples#androidapplication*

``` pro
-dontpreverify 
-repackageclasses '' 
-allowaccessmodification 
# 不优化算法指令
-optimizations !code/simplification/arithmetic 
-keepattributes *Annotation* 

# keep继承自系统组件的类
-keep public class * extends android.app.Activity 
-keep public class * extends android.app.Application 
-keep public class * extends android.app.Service 
-keep public class * extends android.content.BroadcastReceiver 
-keep public class * extends android.content.ContentProvider

# keep自定义view及其构造方法、set方法
-keep public class * extends android.view.View { 
      public <init>(android.content.Context); 
      public <init>(android.content.Context, android.util.AttributeSet); 
      public <init>(android.content.Context, android.util.AttributeSet, int); 
      public void set*(...); 
} 

-keepclasseswithmembers class * { 
    public <init>(android.content.Context, android.util.AttributeSet); 
} 

-keepclasseswithmembers class * { 
    public <init>(android.content.Context, android.util.AttributeSet, int); 
} 

-keepclassmembers class * extends android.content.Context { 
    public void *(android.view.View); 
    public void *(android.view.MenuItem); 
} 

-keepclassmembers class * implements android.os.Parcelable { 
    static ** CREATOR; 
} 

-keepclassmembers class **.R$* { 
    public static <fields>; 
} 

# keep javascript注释的方法，使用到webview js回调方法的需要添加此配置
-keepclassmembers class * { 
    @android.webkit.JavascriptInterface <methods>; 
}
```

# 关于反射

并不是所有会被反射引用的类都必须keep，在progurad过程中能直接分析到引用的类会被proguard做相应的处理：

``` pro
# Class.forName的类名"SomeClass"被混淆后自动替换
Class.forName("SomeClass")
SomeClass.class
# 以下字段和方法名都会在被混淆后自动替换
SomeClass.class.getField("someField")
SomeClass.class.getDeclaredField("someField")
SomeClass.class.getMethod("someMethod", new Class[] {})
SomeClass.class.getMethod("someMethod", new Class[] { A.class })
SomeClass.class.getMethod("someMethod", new Class[] { A.class, B.class })
SomeClass.class.getDeclaredMethod("someMethod", new Class[] {})
SomeClass.class.getDeclaredMethod("someMethod", new Class[] { A.class })
SomeClass.class.getDeclaredMethod("someMethod", new Class[] { A.class, B.class })
AtomicIntegerFieldUpdater.newUpdater(SomeClass.class, "someField")
AtomicLongFieldUpdater.newUpdater(SomeClass.class, "someField")
AtomicReferenceFieldUpdater.newUpdater(SomeClass.class, SomeType.class, "someField")
```

验证一下：

``` java
Class<?> clazz = Class.forName("com.rush.test.SimpleClass1");
clazz.getDeclaredMethod("Test1");
SimpleClass2.class.getDeclaredField("mTestField");
SimpleClass2.class.getDeclaredMethod("Test2");
```

对以上代码编译并proguard，结果如下：

``` java
Class.forName("com.rush.a.a").getDeclaredMethod("Test1", new Class[0]);
b.class.getDeclaredField("a");
b.class.getDeclaredMethod("a", new Class[0]);
```

- 通过Class.forName反射的class com.rush.test.SimpleClass1"被自动替换成了"com.rush.a.a"；
- 但通过Class.forName获取的class再去反射方法没有正确处理；
- 通过完整class.getDeclaredField或者getDeclaredMethod反射时能够把字段名和方法名自动替换掉。

从结果看，反射并不是大家想像的那样必须keep，proguard能自动分析到引用的情况都能正确处理。但有些类是在配置文件里配置，或者动态拼接类名反射的，这些情况需要做好keep。

为了问题追踪的方便，建议所有会被反射引用的代码和library public接口都做好keep。

# 关于proguard配置的一些建议

- 所有会被反射引用的类都做好keep（建议，虽然有些反射能被正确处理）。
  如native方法，四大组件，接口model，枚举，序列化类等。
- 只keep必须保留的内容，不要过度keep
- 使用热修复的App，添加-dontoptimize配置

# 资源混淆

`AndResGuard`是一个帮助你缩小APK大小的工具，他的原理类似Java Proguard，但是只针对资源。他会将原本冗长的资源路径变短，例如将`res/drawable/wechat变为r/d/a`。

`AndResGuard`不涉及编译过程，只需输入一个apk(无论签名与否，`debug`版，`release`版均可，在处理过程中会直接将原签名删除)，可得到一个实现资源混淆后的apk(若在配置文件中输入签名信息，可自动重签名并对齐，得到可直接发布的apk)以及对应资源ID的mapping文件。

github:https://github.com/shwenzhang/AndResGuard/blob/master/README.zh-cn.md

原理:https://mp.weixin.qq.com/s?__biz=MzAwNDY1ODY2OQ==&mid=208135658&idx=1&sn=ac9bd6b4927e9e82f9fa14e396183a8f#rd

# 丧心病狂的混淆操作

原理：混淆是可以配置词典,[原文链接](https://mp.weixin.qq.com/s/ya0RiLuHfIBrPLkl2lTbaA)

随便找一个开源项目上手

https://github.com/kingwang666/GetApk

开启混淆

``` gradle
buildTypes {
    debug {
        minifyEnabled true
        proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
    }
}
```

可以看到上面配置了混淆文件包含：`proguard-rules.pro`

和moudule的build.gradle在同一目录，在里面可以添加你的词典配置：

![](demo.png)

>强烈建议，开启混淆后，配置词典前，先打个包运行一下确定可以正常运行。

最后上面的混淆词典，分别来自不同的开源项目：

https://github.com/RockyQu/ProguardDictionary

包含使用Java关键词的词典。

https://github.com/o2e/ProguardDictionaryGenerator

包含最后那个全是非常神奇的字符的。

https://github.com/WrBug/FrenziedProguard

包含1il,中文，0oO的。

>放一个混淆后的apk地址，如果实在懒得run又想看看效果：
>http://wanandroid.com/blogimgs/57ed3c61-08ee-4a3f-b859-f3cd1d748437.apk

# 参考链接

https://www.jianshu.com/p/60e82aafcfd0

https://rockycoder.cn/android/2018/03/15/Android-proguard-rules.html

https://juejin.im/post/5ae7edc7f265da0b776f7a95

https://www.imooc.com/learn/879

https://mp.weixin.qq.com/s/BP3SIDaxAy4-5rZYMibpmA

https://www.guardsquare.com/en/proguard/manual/introduction

https://www.guardsquare.com/en/proguard/manual/usage

https://www.guardsquare.com/en/proguard/manual/examples




