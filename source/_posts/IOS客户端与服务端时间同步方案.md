---
title: IOS客户端与服务端时间同步方案
date: 2020-05-12 10:35:16
keywords:
    - Ios
    - timestamp
    - Swift
tags:
    - Ios
categories:
    - Ios
---

# 前言

最近项目的编写中，接口需要提交精确到秒级的时间戳用作校验。但是仅靠`new Date().timeIntervalSince1970`会面临着本地的时间与服务器时间不一致的问题。那么本文方案能让本地应用时间与服务器时间存在误差范围内保持同步，减少应用出错率。

<!-- more -->

# 预备知识

- 获取设备当前时间 `Now`，该值受系统时间影响，用户如果修改时间，值也会随着变化；
- 获取设备上次重启的时间 `BootTime`，该值受系统时间影响，用户如果修改时间，值也会随着变化；
- 由上面 `iOS` 提供的两个接口，我们可以获取到**设备自上次重启后运行的时间**（`BootTime - Now`），该值与系统时间无关；{% label danger@`-`这个不是减，指的是区间，下面同理 %} 
- 在必要的时刻获取一下服务器时间，然后记录这个时刻的**设备自上次重启后运行的时间**（`BootTime - Now`）
- 后续时间获取：**现在服务器时间 = 以前服务器时间 + 现在设备自上次重启后运行的时间 - 以前服务器时间的获取时刻的设备自上次重启后运行的时间**
- 利用AFNetworking自动同步时间

# 时间的获取

**获取now的Unix Time**

``` swift
class func now() -> Int {
	var now = timeval()
	var tz = timezone()
	gettimeofday(&now, &tz)
	return now.tv_sec
}
```

**获取设备上次重启的 Unix Time**

``` swift
class func boottime() -> Int {
	var mid = [CTL_KERN, KERN_BOOTTIME]
	var boottime = timeval()
	var size = MemoryLayout.size(ofValue: boottime)
  if sysctl(&mid, 2, &boottime, &size, nil, 0) != -1 {
		return boottime.tv_sec
	}
	return 0
}
```

**获取设备自上次重启后运行的时间**

``` swift
now() - boottime()
```

# TimeUtils编写

``` swift
import UIKit

class TimeUtils: NSObject {
    
    private static var isServerTime: Bool = false
    
    private static var diffTime: Int = 0

  	/// 项目中所有获取时间的方法
    class func getServerTime() -> Int {
        objc_sync_enter(self)
        defer {
            objc_sync_exit(self)
        }
        if !isServerTime {
            let t = Date().timestamp
            return t
        }
        let t = diffTime + (now() - boottime())
        return t
    }
    
    /// 用于计算时间
    class func calibrationTime(lastServerTime: Int) -> Int {
        objc_sync_enter(self)
        defer {
            objc_sync_exit(self)
        }
        diffTime = lastServerTime - (now() - boottime())
        isServerTime = true
        return lastServerTime
    }
    
    /// 获取当前 Unix Time
    class func now() -> Int {
        var now = timeval()
        var tz = timezone()
        gettimeofday(&now, &tz)
        return now.tv_sec
    }
    
  	/// 获取设备上次重启的 Unix Time
    class func boottime() -> Int {
        var mid = [CTL_KERN, KERN_BOOTTIME]
        var boottime = timeval()
        var size = MemoryLayout.size(ofValue: boottime)
        
        if sysctl(&mid, 2, &boottime, &size, nil, 0) != -1 {
            return boottime.tv_sec
        }
        return 0
    }
    
}

extension Date {
    
    var timestamp: Int {
        let timeInterval: TimeInterval = self.timeIntervalSince1970
        return Int(timeInterval)
    }
    
}
```

# 利用AFNetworking同步时间

利用AFN发起请求返回时通过HTTP Header来获取服务器时间，时间格式以[RFC-7231](https://link.jianshu.com/?t=https%3A%2F%2Ftools.ietf.org%2Fhtml%2Frfc7231%23section-7.1.1.1)中定义的"HTTP日期"格式来发送，解析该时间需要用到以下的OC代码，Swift项目请自行用`Bridging-Header`

**NSDate+InternetDateTime.h**

``` objc
//
//  NSDate+InternetDateTime.h
//  MWFeedParser
//
//  Created by Michael Waterfall on 07/10/2010.
//  Copyright 2010 Michael Waterfall. All rights reserved.
//

#import <Foundation/Foundation.h>

// Formatting hints
typedef enum {
    DateFormatHintNone, 
    DateFormatHintRFC822, 
    DateFormatHintRFC3339
} DateFormatHint;

// A category to parse internet date & time strings
@interface NSDate (InternetDateTime)

// Get date from RFC3339 or RFC822 string
// - A format/specification hint can be used to speed up, 
//   otherwise both will be attempted in order to get a date
+ (NSDate *)dateFromInternetDateTimeString:(NSString *)dateString 
                                formatHint:(DateFormatHint)hint;

// Get date from a string using a specific date specification
+ (NSDate *)dateFromRFC3339String:(NSString *)dateString;
+ (NSDate *)dateFromRFC822String:(NSString *)dateString;

@end
```

**NSDate+InternetDateTime.m**

``` objc
//
//  NSDate+InternetDateTime.m
//  MWFeedParser
//
//  Created by Michael Waterfall on 07/10/2010.
//  Copyright 2010 Michael Waterfall. All rights reserved.
//

#import "NSDate+InternetDateTime.h"

// Always keep the formatter around as they're expensive to instantiate
static NSDateFormatter *_internetDateTimeFormatter = nil;

// Good info on internet dates here:
// http://developer.apple.com/iphone/library/qa/qa2010/qa1480.html
@implementation NSDate (InternetDateTime)

// Instantiate single date formatter
+ (NSDateFormatter *)internetDateTimeFormatter {
    @synchronized(self) {
        if (!_internetDateTimeFormatter) {
            NSLocale *en_US_POSIX = [[NSLocale alloc] initWithLocaleIdentifier:@"en_US_POSIX"];
            _internetDateTimeFormatter = [[NSDateFormatter alloc] init];
            [_internetDateTimeFormatter setLocale:en_US_POSIX];
            [_internetDateTimeFormatter setTimeZone:[NSTimeZone timeZoneForSecondsFromGMT:0]];
        }
    }
    return _internetDateTimeFormatter;
}

// Get a date from a string - hint can be used to speed up
+ (NSDate *)dateFromInternetDateTimeString:(NSString *)dateString formatHint:(DateFormatHint)hint {
     // Keep dateString around a while (for thread-safety)
	NSDate *date = nil;
    if (dateString) {
        if (hint != DateFormatHintRFC3339) {
            // Try RFC822 first
            date = [NSDate dateFromRFC822String:dateString];
            if (!date) date = [NSDate dateFromRFC3339String:dateString];
        } else {
            // Try RFC3339 first
            date = [NSDate dateFromRFC3339String:dateString];
            if (!date) date = [NSDate dateFromRFC822String:dateString];
        }
    }
     // Finished with date string
	return date;
}

// See http://www.faqs.org/rfcs/rfc822.html
+ (NSDate *)dateFromRFC822String:(NSString *)dateString {
     // Keep dateString around a while (for thread-safety)
    NSDate *date = nil;
    if (dateString) {
        NSDateFormatter *dateFormatter = [NSDate internetDateTimeFormatter];
        @synchronized(dateFormatter) {

            // Process
            NSString *RFC822String = [[NSString stringWithString:dateString] uppercaseString];
            if ([RFC822String rangeOfString:@","].location != NSNotFound) {
                if (!date) { // Sun, 19 May 2002 15:21:36 GMT
                    [dateFormatter setDateFormat:@"EEE, d MMM yyyy HH:mm:ss zzz"]; 
                    date = [dateFormatter dateFromString:RFC822String];
                }
                if (!date) { // Sun, 19 May 2002 15:21 GMT
                    [dateFormatter setDateFormat:@"EEE, d MMM yyyy HH:mm zzz"]; 
                    date = [dateFormatter dateFromString:RFC822String];
                }
                if (!date) { // Sun, 19 May 2002 15:21:36
                    [dateFormatter setDateFormat:@"EEE, d MMM yyyy HH:mm:ss"]; 
                    date = [dateFormatter dateFromString:RFC822String];
                }
                if (!date) { // Sun, 19 May 2002 15:21
                    [dateFormatter setDateFormat:@"EEE, d MMM yyyy HH:mm"]; 
                    date = [dateFormatter dateFromString:RFC822String];
                }
            } else {
                if (!date) { // 19 May 2002 15:21:36 GMT
                    [dateFormatter setDateFormat:@"d MMM yyyy HH:mm:ss zzz"]; 
                    date = [dateFormatter dateFromString:RFC822String];
                }
                if (!date) { // 19 May 2002 15:21 GMT
                    [dateFormatter setDateFormat:@"d MMM yyyy HH:mm zzz"]; 
                    date = [dateFormatter dateFromString:RFC822String];
                }
                if (!date) { // 19 May 2002 15:21:36
                    [dateFormatter setDateFormat:@"d MMM yyyy HH:mm:ss"]; 
                    date = [dateFormatter dateFromString:RFC822String];
                }
                if (!date) { // 19 May 2002 15:21
                    [dateFormatter setDateFormat:@"d MMM yyyy HH:mm"]; 
                    date = [dateFormatter dateFromString:RFC822String];
                }
            }
            if (!date) NSLog(@"Could not parse RFC822 date: \"%@\" Possible invalid format.", dateString);
            
        }
    }
     // Finished with date string
    return date;
}

// See http://www.faqs.org/rfcs/rfc3339.html
+ (NSDate *)dateFromRFC3339String:(NSString *)dateString {
     // Keep dateString around a while (for thread-safety)
    NSDate *date = nil;
    if (dateString) {
        NSDateFormatter *dateFormatter = [NSDate internetDateTimeFormatter];
        @synchronized(dateFormatter) {
            
            // Process date
            NSString *RFC3339String = [[NSString stringWithString:dateString] uppercaseString];
            RFC3339String = [RFC3339String stringByReplacingOccurrencesOfString:@"Z" withString:@"-0000"];
            // Remove colon in timezone as it breaks NSDateFormatter in iOS 4+.
            // - see https://devforums.apple.com/thread/45837
            if (RFC3339String.length > 20) {
                RFC3339String = [RFC3339String stringByReplacingOccurrencesOfString:@":" 
                                                                         withString:@"" 
                                                                            options:0
                                                                              range:NSMakeRange(20, RFC3339String.length-20)];
            }
            if (!date) { // 1996-12-19T16:39:57-0800
                [dateFormatter setDateFormat:@"yyyy'-'MM'-'dd'T'HH':'mm':'ssZZZ"]; 
                date = [dateFormatter dateFromString:RFC3339String];
            }
            if (!date) { // 1937-01-01T12:00:27.87+0020
                [dateFormatter setDateFormat:@"yyyy'-'MM'-'dd'T'HH':'mm':'ss.SSSZZZ"]; 
                date = [dateFormatter dateFromString:RFC3339String];
            }
            if (!date) { // 1937-01-01T12:00:27
                [dateFormatter setDateFormat:@"yyyy'-'MM'-'dd'T'HH':'mm':'ss"]; 
                date = [dateFormatter dateFromString:RFC3339String];
            }
            if (!date) NSLog(@"Could not parse RFC3339 date: \"%@\" Possible invalid format.", dateString);
            
        }
    }
     // Finished with date string
	return date;
}

@end
```

> 来源：https://github.com/mwaterfall/MWFeedParser/blob/master/Classes/NSDate+InternetDateTime.m（ARC）

在项目中封装一个AFN的请求基类

```swift
import UIKit
import AFNetworking

enum HttpMethod: String {
    case GET = "GET"
    case POST = "POST"
}
 
class HttpClient: NSObject {
    
    static let baseURLString = "http://base.url/"
    static var minResponseTime = Int.max
 
    static let sharedClient: AFHTTPSessionManager = {
        let baseUrl = URL(string: baseURLString)
        let client = AFHTTPSessionManager(baseURL: baseUrl!)
        // 设置请求格式
        client.requestSerializer = AFJSONRequestSerializer()
        // 设置返回格式
        client.responseSerializer = AFJSONResponseSerializer()
        // 设置禁止缓存
        client.requestSerializer.cachePolicy = .reloadIgnoringLocalCacheData
        // 设置超时
        client.requestSerializer.timeoutInterval = 10
        // 以防解析格式不支持
        client.responseSerializer.acceptableContentTypes?.insert("text/html")
        client.responseSerializer.acceptableContentTypes?.insert("text/plain")
        return client
    }()

}

extension HttpClient {
    
    // MARK: 请求方式
    
    /// 项目公共请求类
    /// - Parameters:
    ///   - httpMethod: httpMethod
    ///   - URLString: URLString
    ///   - parameters: 参数
    ///   - headers: 请求头
    ///   - progress: 进度
    ///   - success: 成功回调
    ///   - failure: 失败回调
    private class func request(httpMethod: HttpMethod, URLString: String, parameters: [String : Any]?,
                       headers: [String : String]?, progress: ((Progress) -> Void)?,
                       success: ((URLSessionDataTask, Any?) -> Void)?, failure: ((URLSessionDataTask?, Error) -> Void)?) {
        // 记录当前请求开始时间
        let startTime = Date().timestamp
        
        // 定义通用成功回调
        let successCallback = { (task: URLSessionDataTask, result: Any?) -> () in
            // 当前请求响应的时间间隔
            let responseTime = Date().timestamp - startTime
            //如果这一次的请求响应时间小于上一次，则更新本地维护的时间
            if responseTime <= minResponseTime {
                if let response = task.response as? HTTPURLResponse {
                    // 网络响应头包含Date字段（世界时间）
                    if response.allHeaderFields["Date"] is String {
                        let dateString = response.allHeaderFields["Date"]! as! String
                        if let date = NSDate(fromInternetDateTime: dateString, formatHint: DateFormatHintRFC822) {
                            let _ = TimeUtils.calibrationTime(lastServerTime: Int(date.timeIntervalSince1970))
                            minResponseTime = responseTime
                        }
                    }
                }
            }
            success?(task, result)
        }
        
        if httpMethod == .GET {
            self.sharedClient.get(URLString, parameters: parameters, headers: safeHeaders, progress: progress, success: successCallback, failure: failure)
            return
        }
        
        if httpMethod == .POST {
            self.sharedClient.post(URLString, parameters: parameters, headers: safeHeaders, progress: progress, success: successCallback, failure: failure)
        }
    }
    
}
```

{% note warning %}
该方案下前提必须禁止AFN自带的缓存策略`client.requestSerializer.cachePolicy = .reloadIgnoringLocalCacheData`。不禁止会出现时间异常的现象，如项目中是使用AFN自带的缓存策略则需要更换项目中的缓存方案。
{% endnote %}

> 该方案都仅到秒级，如需到毫秒级请做*1000处理
>
> 不足：连接服务器的过程是需要时间的，服务器收到请求时刻的时间与应用收到响应存在一定的时间差，导致误差的存在（误差=服务器发出响应->到本机收到响应这个时间）。
>
> 但是通过上面的AFN每次判断，可以使得误差逐渐降低

# 参考资料

https://www.jianshu.com/p/48ac0bb1392e

https://www.jianshu.com/p/bb2cbfad3e77

https://www.jianshu.com/p/df41659b06a9

