---
title: IOS中JSON序列化与反序列化
date: 2019-03-06 17:46:47
keywords:
    - JSON
    - NSJSONSerialization
    - MJExtension
tags:
    - Ios
categories:
    - Ios
---

# 序列化与反序列化

序列化： 将数据结构或对象转换成二进制串的过程。
反序列化：将在序列化过程中所生成的二进制串转换成数据结构或者对象的过程。

<!-- more -->

# NSJSONSerialization

## json->oc

一段服务器的json数据

``` json
{
    "data":"hello",
    "code":200,
    "msg":"success"
}
```

二进制数据转json,方法
``` objc
/*
  * 第一个参数：json的二进制数据
  * 第二个参数：
  * NSJSONReadingMutableContainers = (1UL << 0),得到OC对象是可变的
  * NSJSONReadingMutableLeaves = (1UL << 1), 字典和数组中的字符串都是可变的，iOS7以后出现很多问题，一般不会用到
  * NSJSONReadingAllowFragments = (1UL << 2) 既不是字典也不是数组，则必须使用该枚举值 如果返回数据为@“\"hahahahaha\"”
  * 第三个参数：错误信息
  */
    NSDictionary *dic = [NSJSONSerialization JSONObjectWithData:data options:kNilOptions error:nil];
```

举例

``` objc
NSURL *url = [NSURL URLWithString:@"http://127.0.0.1:8000/hello/name"];
    
    NSURLRequest *request = [NSURLRequest requestWithURL:url];
    [NSURLConnection sendAsynchronousRequest:request queue:[NSOperationQueue mainQueue] completionHandler:^(NSURLResponse * _Nullable response, NSData * _Nullable data, NSError * _Nullable connectionError) {
        if (connectionError) {
            NSLog(@"%@", connectionError);
            return;
        }
        NSHTTPURLResponse *res = (NSHTTPURLResponse *) response;
        if (res.statusCode == 200) {
            //开始解析json
            NSError *error = nil;
            id json = [NSJSONSerialization JSONObjectWithData:data options:NSJSONReadingMutableContainers error:&error];
            if (error) {
                NSLog(@"%@", error);
                return;
            }
            NSLog(@"%@", json);
        }else{
            NSLog(@"服务器错误");
        }
    }];
```
输出结果
``` log
2019-03-06 17:44:34.480939+0800 JsonDemo[5349:107008] {
    code = 200;
    data = hello;
    msg = success;
}
```

## oc->json

举例
``` objc
/* 注意：并不是所有的OC对象都可以转化为json 例如字符串
     * 顶层必须是NSArray或者NSDictionnary
     * 所有的元素必须是 NSString, NSNumber, NSArray, NSDictionary, or NSNull
     * 字典中所有的key必须是NSStrings类型的
     * NSNumbers 不能是无穷大
     */
    // 1、提供转化的OC字典
    NSDictionary *dic = @{
                          @"code":@200,
                          @"data":[NSNull null],
                          @"msg":@"oh my json"
                          };
    // 2、判断OC对象是否可以转换为json对象
    BOOL isVaild = [NSJSONSerialization isValidJSONObject:dic];
    if (isVaild) {
        /*
         * 第一个参数：需要序列化的数据
         * 第二个参数：如果添加就输出排版美观效果 如果不添加按照默认输出
         *        NSJSONWritingPrettyPrinted = (1UL << 0)
         */
        NSData *data = [NSJSONSerialization dataWithJSONObject:dic options:NSJSONWritingPrettyPrinted error:nil];
        NSLog(@"%@",[[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);
    }else{
        
        NSLog(@"该对象无法转换");
    }
```
控制台输出
``` log
2019-03-06 17:57:27.734633+0800 JsonDemo[6202:131086] {
  "msg" : "oh my json",
  "data" : null,
  "code" : 200
}
```

# MJExtension

##  安装

### CocoaPods

``` bash
pod 'MJExtension'
```

### 手动导入

github[链接](https://github.com/CoderMJLee/MJExtension)

## 使用

### 字典转模型

模型USER
``` objc
@interface User : NSObject

@property(copy, nonatomic)NSString *name;

@property(copy, nonatomic)NSString *zhiye;

@property(copy, nonatomic)NSString *money;

@end
```

``` objc
NSDictionary *dict = @{
                           @"name":@"ycy",
                           @"zhiye":@"程序员",
                           @"money":@"-4000"
                           };
    User *user = [User mj_objectWithKeyValues:dict];
    NSLog(@"%@",user.name);
```

### JSONString转模型

``` objc
// 1.Define a JSONString
NSString *jsonString = @"{\"name\":\"Jack\", \"icon\":\"lufy.png\", \"age\":20}";

// 2.JSONString -> User
User *user = [User mj_objectWithKeyValues:jsonString];

// 3.Print user's properties
NSLog(@"name=%@, icon=%@, age=%d", user.name, user.icon, user.age);
// name=Jack, icon=lufy.png, age=20
```

### more

查看官方文档即可[链接](https://github.com/CoderMJLee/MJExtension)



