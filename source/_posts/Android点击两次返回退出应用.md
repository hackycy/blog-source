---
title: Android点击两次返回退出应用
date: 2018-10-30 09:55:41
keywords:
    - Android
tags:
    - Android
categories:
    - Android
---

在一些app中，一般都是主页面都会有一个连续点击两次返回键就能够退出，也是防止返回键误触导致直接退出了应用。示例没有复杂的activity管理，点击两次后直接finish就完成了。

<!-- more -->

# 思路

直接判断用户两次按键的时间差是否在一个预期值之内，是则退出，否则应该出现一些提示来提醒用户。

``` Java
public class MainActivity extends BaseActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    //记录用户首次点击返回键的时间
    private long mExitFirstTime = 0;
    //双击间隔
    private long mExitIntervalMs = 2000;

    @Override
    public boolean onKeyUp(int keyCode, KeyEvent event) {
        switch (keyCode){
            case KeyEvent.KEYCODE_BACK:
                long secondTime = System.currentTimeMillis();
                if(secondTime - mExitFirstTime > mExitIntervalMs){
                    mExitFirstTime = secondTime;
                    Toast.makeText(MainActivity.this, R.string.exit_tip, Toast.LENGTH_SHORT)
                            .show();
                    return true; //消费掉该事件
                }else{
                    finish();
                }
                break;
        }
        return super.onKeyUp(keyCode, event);
    }
}
```

> 重写onKeyDown()和onBackPressed()方法都能捕获Back的点击事件，

