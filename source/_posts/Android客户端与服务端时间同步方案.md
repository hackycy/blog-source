---
title: Android客户端与服务端时间同步方案
date: 2020-03-20 11:50:41
keywords:
    - Android
tags:
    - Android
categories:
    - Android
---

# 前言

最近项目的编写中，接口需要提交精确到秒级的时间戳用作校验。但是仅仅使用`System.currentTimeMillis()`会面临着本地的时间与服务器时间不一致的问题。那么本文方案能让本地应用时间与服务器时间存在误差范围内保持同步，减少应用出错率。

<!-- more -->

# 预备知识

- `SystemClock.elapsedRealtime()` ：手机系统开机时间（包含睡眠时间），用户无法在设置里面修改。
- 在必要的时刻获取一下服务器时间，然后记录这个时刻的手机开机时间（elapsedRealtime）
- 后续时间获取：**现在服务器时间 = 以前服务器时间 + 现在手机开机时间 - 以前服务器时间的获取时刻的手机开机时间**
- 利用OkHttp的Interceptor自动同步时间

# TimeUtils编写

``` java
import android.os.SystemClock;
import android.text.TextUtils;

import com.blankj.utilcode.util.LogUtils;

import java.io.IOException;
import java.util.Date;

import okhttp3.Headers;
import okhttp3.Interceptor;
import okhttp3.Request;
import okhttp3.Response;
import okhttp3.internal.http.HttpDate;

public final class TimeUtils {

    // 以前服务器时间 - 以前服务器时间的获取时刻的系统启动时间
    private static long diffTime = 0;

    // 是否是服务器时间
    private static boolean isServerTime = false;

    /**
     * 获取当前时间
     * @return
     */
    public synchronized static long getServerTime() {
        if (!isServerTime) {
            return System.currentTimeMillis() / 1000;
        }
        return (diffTime + SystemClock.elapsedRealtime()) / 1000;
    }

    public synchronized static long calibrationTime(long lastServerTime) {
        // 记录时间差
        diffTime = lastServerTime - SystemClock.elapsedRealtime();
        isServerTime = true;
        return lastServerTime;
    }

  	/**
  	* 利用OkHttp的Interceptor自动同步时间
  	*/
    public static class TimeSyncInterceptor implements Interceptor {

        long minResponseTime = Long.MAX_VALUE;

        @Override
        public Response intercept(Chain chain) throws IOException {
            Request request = chain.request();
            long startTime = System.nanoTime();
            Response proceed = chain.proceed(request);
            long lastTime = System.nanoTime() - startTime;

            Headers headers = proceed.headers();
            calibration(lastTime, headers);
            return proceed;
        }

        private void calibration(long responseTime, Headers headers) {
            if (headers == null) {
                return;
            }
            //如果这一次的请求响应时间小于上一次，则更新本地维护的时间
            if (responseTime >= minResponseTime) {
                return;
            }
          	// 网络响应头包含Date字段（世界时间）
          	// 利用Interceptor记录每次请求响应时间，如果本次网络操作的时间小于上一次网络操作的时间，则获取Date字段，转换时区后更新本地TimeUtils。
            String standardTime = headers.get("Date");
            if (!TextUtils.isEmpty(standardTime)) {

                Date parse = HttpDate.parse(standardTime);

                if (parse != null) {
                    // 客户端请求过程一般大于比收到响应时间耗时，所以没有简单的除2 加上去，而是直接用该时间
                    TimeUtils.calibrationTime(parse.getTime());
                    minResponseTime = responseTime;
                }
            }
        }

    }

}
```

> 原理就是通过HTTP Header来获取服务器时间（注：时间格式以[RFC-7231](https://link.jianshu.com/?t=https%3A%2F%2Ftools.ietf.org%2Fhtml%2Frfc7231%23section-7.1.1.1)中定义的"HTTP日期"格式来发送）
>
> 在OkHttpClient增加该Utils下的拦截器即可。
>
> 由于项目中只需要精确到秒，而获取的都是精确到毫秒级别，所以TimeUtils下获取的时候做了除以1000处理。可能会存在点误差吧。但是在项目中误差范围不大，还在可接受范围内。
>
> 不足：连接服务器的过程是需要时间的，服务器收到请求时刻的时间与应用收到响应存在一定的时间差，导致误差的存在（误差=服务器发出响应->到本机收到响应这个时间）。
>
> 但是通过上面的TimeSyncInterceptor每次判断，可以使得误差逐渐降低

# 参考资料

https://blog.csdn.net/qinci/article/details/70666631