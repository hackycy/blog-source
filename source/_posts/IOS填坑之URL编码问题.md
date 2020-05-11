---

title: IOS填坑之URL编码问题
date: 2020-05-08 11:24:06
keywords:
    - Ios
    - url编码
    - Swift
tags:
    - Ios
categories:
    - Ios
---

# 前言

在使用WKWebview的时候，通常都离不开URL，一般的符合网络标准的URL没有什么问题，但是在公司开发的时候遇到了一些特殊URL的时候就踩到了URL编码的坑。

在1994年订制的RFC1738文档中，对字符串中的除了`- _ .`之外的所有非字母数字字符都替换成百分号(%)后跟两位十六进制数，十六进制数中字母必须为大写。

在2005年定义的RFC3986中，将针对`- _.~`四个字符之外的所有非字母数字字符进行百分号编码。当然 根据URL的类型不同，有也一部分预留字符不需要进行编码，例如查询的`URL`中可以包含`? /`字符，不需要转义。更详细文档的可以查看[RFC 3986](http://www.ietf.org/rfc/rfc3986.txt)。

<!-- more -->

# Swift URL Encode

`addingPercentEncoding(withAllowedCharacters:`是iOS7之后出现的新API用于`url encode`。

官方对该方法的解释：

> // Returns a new string made from the receiver by replacing all characters not in the allowedCharacters set with percent encoded characters. UTF-8 encoding is used to determine the correct percent encoded characters. Entire URL strings cannot be percent-encoded. This method is intended to percent-encode an URL component or subcomponent string, NOT the entire URL string. Any characters in allowedCharacters outside of the 7-bit ASCII range are ignored.  

最后一句Any characters in allowedCharacters outside of the 7-bit ASCII range are ignored.，意思就是说，任何非7-bit ASCII字符搁到allowedCharacters里面也将被忽略，也就是`allowedCharacters`里面的字符跟7-bit ASCII字符不会被编码。

换句话说，上面方法在处理的时候会编码url的中的非7-bit ASCII字符，如这些【\`#%^{}"[]|\<>】，如果需要忽略之，需要通过.allowedCharacters这个参数指定。

然而并不是，我们来看一下案例：

``` swift
let url1 = "http://github.com#aaa?name=中文字符&key=!*'();:@&=+$,/?%#[]"
print(url1.addingPercentEncoding(withAllowedCharacters: CharacterSet(charactersIn: "#%^{}\"[]|\\<>"))!)
```

输出结果：

``` bash
%68%74%74%70%3A%2F%2F%67%69%74%68%75%62%2E%63%6F%6D#%61%61%61%3F%6E%61%6D%65%3D%E4%B8%AD%E6%96%87%E5%AD%97%E7%AC%A6%26%6B%65%79%3D%21%2A%27%28%29%3B%3A%40%26%3D%2B%24%2C%2F%3F%#[]
Program ended with exit code: 0
```

可以看到只有后面的%#[]没有被编码，其余都被编码了，**不是我们之前所理解的“allowedCharacters里面的字符跟7-bit ASCII字符不会被编码”，而是只有allowedCharacters里的字符才不会被编码**

正确的方法：使用`inverted`

``` swift
let url1 = "http://github.com#aaa?name=中文字符&key=!*'();:@&=+$,/?%#[]"
print(url1.addingPercentEncoding(withAllowedCharacters: CharacterSet(charactersIn: "#%^{}\"[]|\\<>").inverted)!)
```

输出结果：

``` bash
let url1 = "http://github.com#aaa?name=中文字符&key=!*'();:@&=+$,/?%#[]"
print(url1.addingPercentEncoding(withAllowedCharacters: CharacterSet(charactersIn: "#%^{}\"[]|\\<>").inverted)!)
```

可以看到，通过集合反转之后得到的结果才是我们想要的，但是此处的意思是反的，就是**对集合进行inverted，表示集合内的字符和非7-bit ASCII字符是需要转码**的，所以我们以后使用这个方法进行转码的时候要从反面进行转码，把想要进行转码的特殊字符写在集合里就好了，注意这里说的是想要转码的特殊字符（**!\*'();:@&=+$,/?%#[]**），中文会被认为是非7-bit ASCII字符会自动转码的。

## CharacterSet

`CharacterSet`是一个结构体，`CharacterSet.urlHostAllowed`等预制类型包含了所有不需要被转码的字符，**反过来说就是指明了需要被转码的字符**。`CharacterSet`类中提供了一些常用的URL转码的类型:

```swift
CharacterSet.urlHostAllowed: 被转义的字符有  "#%/<>?@\^`{|}
CharacterSet.urlPathAllowed: 被转义的字符有  "#%;<>?[\]^`{|}
CharacterSet.urlUserAllowed: 被转义的字符有   "#%/:<>?@[\]^`
CharacterSet.urlQueryAllowed: 被转义的字符有  "#%<>[\]^`{|}
CharacterSet.urlPasswordAllowed 被转义的字符有 "#%/:<>?@[\]^`{|}
CharacterSet.urlFragmentAllowed 被转义的字符有 "#%<>[\]^`{|}
```

## 使用

### 编码

``` swift
let url1 = "http://github.com?name=中文字符&key=!*'();:@&=+$,/?%#[]"
print(url1.addingPercentEncoding(withAllowedCharacters: .urlQueryAllowed)!)
```

输出结果：

``` bash
http://github.com?name=%E4%B8%AD%E6%96%87%E5%AD%97%E7%AC%A6&key=!*'();:@&=+$,/?%25%23%5B%5D
Program ended with exit code: 0
```

### 解码

``` swift
var encodeUrl1 = "http://github.com%23aaa?name=%E4%B8%AD%E6%96%87%E5%AD%97%E7%AC%A6&key=!*'();:@&=+$,/?%25%23%5B%5D"
print(encodeUrl1.removingPercentEncoding!)
```

输出结果：

``` bash
http://github.com#aaa?name=中文字符&key=!*'();:@&=+$,/?%#[]
Program ended with exit code: 0
```

# URL带有#符号的问题

\#是url中的一个重要组成部分，是跟在url参数之后的的最后一部分，作为一个url的锚点，用于浏览器的定位。

但是在项目中使用的时候发现#号被转义掉了，前端那边就没有办法正常显示。所以需要其他特殊字符正常转义，除去#号。

## 方法一

沿用预制类型，采用insert方法

``` swift
var urlStr = "http://test.com/中文/main.html#/help"
var charSet = CharacterSet.urlQueryAllowed
charSet.insert(charactersIn: "#")
let encodingURL = urlStr.addingPercentEncoding(withAllowedCharacters: .urlQueryAllowed)!
print(encodingURL)
```

这里采用了预制的，insert会反过来被删除掉

> 至于insert的原因，可以查看一下以下代码
>
> ``` swift
> let url1 = "http://github.com#aaa?name=中文字符&key=!*'();:@&=+$,/?%#[]"
> print("-----------------未反转情况-------------------------")
> var s1 = CharacterSet(charactersIn: #"[]"#)
> print(url1.addingPercentEncoding(withAllowedCharacters: s1.inverted)!)
> s1.insert(charactersIn: "[")
> print(url1.addingPercentEncoding(withAllowedCharacters: s1.inverted)!)
> 
> print("-----------------反转情况-------------------------")
> var s2 = CharacterSet(charactersIn: #"[]"#).inverted
> print(url1.addingPercentEncoding(withAllowedCharacters: s2)!)
> s2.insert(charactersIn: "[")
> print(url1.addingPercentEncoding(withAllowedCharacters: s2)!)
> ```
>
> 输出结果，留意最后的[]符号是否被转义
>
> ``` bash
> -----------------未反转情况-------------------------
> http://github.com#aaa?name=%E4%B8%AD%E6%96%87%E5%AD%97%E7%AC%A6&key=!*'();:@&=+$,/?%#%5B%5D
> http://github.com#aaa?name=%E4%B8%AD%E6%96%87%E5%AD%97%E7%AC%A6&key=!*'();:@&=+$,/?%#%5B%5D
> -----------------反转情况-------------------------
> http://github.com#aaa?name=%E4%B8%AD%E6%96%87%E5%AD%97%E7%AC%A6&key=!*'();:@&=+$,/?%#%5B%5D
> http://github.com#aaa?name=%E4%B8%AD%E6%96%87%E5%AD%97%E7%AC%A6&key=!*'();:@&=+$,/?%#[%5D
> Program ended with exit code: 0
> ```
>
> 还未找到解释理由

## 方法二

自定义

``` swift
var urlStr = "http://test.com/中文/main.html#/help"
let encodingURL = urlStr.addingPercentEncoding(withAllowedCharacters: CharacterSet(charactersIn: #""%<>[\]^`{|}"#).inverted)!
print(encodingURL)
```

# Json的URL编码

我公司有的链接需要拼接上一大段的json，里面json还有复杂的&等符号，预制的肯定不够用，只能采用自定义方法：

所以封装了方法：

``` swift
extension String {
    
    func urlEncoding() -> String {
        let toSearchword = (self as NSString).addingPercentEncoding(withAllowedCharacters: CharacterSet(charactersIn: #"?!@#$^&%*+,:;='"`<>()[]{}/\|"#).inverted)
        return toSearchword as String? ?? ""
    }
    
}
```

直接使用即可

``` swift
var urlStr = "http://test.com/中文/main.html#/help"
print(urlStr.urlEncoding())
```

# 题外话 

## Swift 5 字符串转义字符处理

增加了 # 符号，使得写字符串更加简单。

**在字符串中包含 " 时不必再加 \ **

```tsx
//before
let rain = "The is\"new\"string" 
//after
let rain = #"The is"new"string"#
```

**包含 \ 反斜杠也不需要再加转义符**

```csharp
//before
let rain = "The is\\new string" 
//after
let rain = #"The is \new string"#
```

**由于反斜杠作为字符串中的字符，所以在插入值的时候需要在后面再加个 #**

```csharp
//before
let age = 26
let dontpanic = "myAge is \(age)"
// after
let answer = 26
let dontpanic = #"myAge is \#(age)"#
```

**当字符串包含 # 时， 前后应用 ## 包裹字符串**

```rust
let str = ##"this is "a"#good ideal"##   
```

**用 #""" 开头 """#结尾 来表示多行字符串**

```bash
let multiline = #"""
The answer to life,
the universe,
and everything is \#(answer).
"""#
```

**由于不用反斜杠转义 使得正则表达式更加简洁明了**

```csharp
//before
let regex1 = "\\\\[A-Z]+[A-Za-z]+\\.[a-z]+"
//after
let regex2 = #"\\[A-Z]+[A-Za-z]+\.[a-z]+"#
```

## Objective-C url encode

API调用都是一样的,不过网上流传的比较多的是用的`C API`

``` objective-c
NSString *ciphertext = @"saf#*&";        
NSCharacterSet *set = [[NSCharacterSet characterSetWithCharactersInString:@"!*'();:@&=+$,/?%#[]"] invertedSet];
NSString *resultString = [ciphertext stringByAddingPercentEncodingWithAllowedCharacters: set];
```

C API

``` c
NSString *ciphertext = @"saf#*&";
NSString *encodedStr = (NSString *)CFBridgingRelease(CFURLCreateStringByAddingPercentEscapes
                                                     (kCFAllocatorDefault,
                                                      (CFStringRef)ciphertext,
                                                      NULL,
                                                      CFSTR("!*'();:@&=+$,/?%#[]"),
                                                      kCFStringEncodingUTF8));
```

# 参考资料

https://www.jianshu.com/p/c135127a3df2

https://www.jianshu.com/p/74f7c5bbca50

https://www.hangge.com/blog/cache/detail_1583.html

https://www.cnblogs.com/luoxiaofu/p/7110011.html

