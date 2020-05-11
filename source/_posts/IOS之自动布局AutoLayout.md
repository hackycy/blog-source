---
title: IOS之自动布局AutoLayout
date: 2019-03-04 10:37:21
keywords:
    - Ios
    - AutoLayout
tags:
    - Ios
categories:
    - Ios
---

# AutoLayout自动布局介绍

UI布局对于iOS开发者来说并不陌生，在iOS6之前，大家都是通过UI控件的Frame属性和Autoresizing Mask来进行UI布局的。AutoLayout则是苹果公司在iOS6推出的一种基于约束的，描述性的布局系统。自从AutoLayout问世以来，逐步得到了iOS开发者们的青睐，尤其是iPhone6机型尺寸的出现。

<!-- more -->

AutoLayout占据UI布局的主要领导位置依赖于它的特殊性：

- 基于约束：和以往定义frame的位置和尺寸不同，AutoLayout的位置确定是以所谓相对位置的约束来定义的，比如x坐标为superView的中心，y坐标为屏幕底部上方10像素等
- 描述性： 约束的定义和各个view的关系使用接近自然语言或者可视化语言（稍后会提到）的方法来进行描述
- 布局系统：即字面意思，用来负责界面的各个元素的位置。

# AutoLayout使用原理

## 创建约束

IOS6中新加入了一个类，`NSLayoutConstraint`,它的约束满足一个公式

``` txt
item1.attribute = multiplier ⨉ item2.attribute + constant
```

相应公式的代码为
``` objc
//view_1(红色)top 距离self.view的top
NSLayoutConstraint *view_1TopToSuperViewTop = [NSLayoutConstraint constraintWithItem:view_1
                                  attribute:NSLayoutAttributeTop
                                  relatedBy:NSLayoutRelationEqual
                                  toItem:self.view
                                  attribute:NSLayoutAttributeTop
                                  multiplier:1
                                  constant:30];
```

这里对应的约束是“view_1的顶部（y）＝ self.view的顶部(y)*1 + 30”。

## 添加约束

在创建约束后，需要将其添加到作用的view上。UIView添加约束的实例方法

``` objc
- (void)addConstraint:(NSLayoutConstraint *)constraint NS_AVAILABLE_IOS(6_0);
- (void)addConstraints:(NSArray<__kindof NSLayoutConstraint *> *)constraints NS_AVAILABLE_IOS(6_0); 
```

UIView中的一个属性`translatesAutoresizingMaskIntoConstraints`

``` objc
//如果你定义的view想用autolayout，就将
translatesAutoresizingMaskIntoConstraints = NO;
//如果你使用的不是autolayout，就将
translatesAutoresizingMaskIntoConstraints = YES;
```

约束的属性

| 名称    |   autolayout 对应属性    |          意义 |
| ------- | :----------------------: | ------------: |
| left    |  NSLayoutAttributeLeft   |        左边框 |
| top     |   NSLayoutAttributeTop   |        上边框 |
| right   |  NSLayoutAttributeRight  |        右边框 |
| bottom  | NSLayoutAttributeBottom  |        下边框 |
| centerX | NSLayoutAttributeCenterX | center的X坐标 |
| centerY | NSLayoutAttributeCenterY | center的Y坐标 |
| width   |  NSLayoutAttributeWidth  |          宽度 |
| height  | NSLayoutAttributeHeight  |          高度 |
| leading              |       NSLayoutAttributeLeading        |       正常情况下等同于left |
| trailing             |       NSLayoutAttributeTrailing       |      正常情况下等同于right |
| baseline             |       NSLayoutAttributeBaseline       |                   对齐基线 |
| leftMargin           |      NSLayoutAttributeLeftMargin      |               左边的Margin |
| rightMargin          |     NSLayoutAttributeRightMargin      |               右边的Margin |
| topMargin            |      NSLayoutAttributeTopMargin       |               顶部的Margin |
| bottomMargin         |     NSLayoutAttributeBottomMargin     |               底部的Margin |
| leadingMargin        |    NSLayoutAttributeLeadingMargin     | 前导（基本等于left）Margin |
| trailingMargin       |    NSLayoutAttributeTrailingMargin    | 后尾（基本等于tail）Margin |
| centerXWithinMargins | NSLayoutAttributeCenterXWithinMargins |            中心X坐标Margin |
| centerYWithinMargins | NSLayoutAttributeCenterYWithinMargins |            中心Y坐标Margin |

当然，如何添加也有相应的规则。

### 对其他的view没有任何关系时，添加到自己的view上

比如说添加一个按钮的宽和高的约束，对其他的view没有任何关系，则将约束添加到自己身上。

为按钮添加一个宽和高为两百的约束
``` objc
#import "ViewController.h"

@interface ViewController ()
@property (strong, nonatomic) IBOutlet UIButton *btn1;
@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    NSLayoutConstraint *c1 = [NSLayoutConstraint constraintWithItem:self.btn1 attribute:NSLayoutAttributeWidth relatedBy:NSLayoutRelationEqual toItem:nil attribute:NSLayoutAttributeWidth multiplier:1 constant:200];
    
    NSLayoutConstraint *c2 = [NSLayoutConstraint constraintWithItem:self.btn1 attribute:NSLayoutAttributeHeight relatedBy:NSLayoutRelationEqual toItem:nil attribute:NSLayoutAttributeHeight multiplier:1 constant:200];
    
    self.btn1.translatesAutoresizingMaskIntoConstraints = NO;
    [self.btn1 addConstraint:c1];
    [self.btn1 addConstraint:c2];
}
@end
```

### 对于两个同层级view之间的约束关系，则添加到他们的父View上

例如`3.1`与`3.2`为同层级view，约束关系则添加到父view`2.1`上。

![](auto1.png)

添加了一个蓝色view的上边线与绿色view的下边线对其的约束，两个view为同级约束，则添加到了父view上。

![](auto2.png)

### 对于两个不同层级的view之间的约束关系，则添加到它们最近的共同的父view上

例如`3.3`与`3.5`为同层级view，约束关系则添加到父view`1`上。

![](auto3.png)

但是相对于这种情况应该用的很少。
### 对于两个有层次关系的view之间的约束关系，则添加到层次较高的那个view上

例如`1`和`2.3`为父子关系的view，约束关系则添加到view`1`上

![](auto5.png)

# 刷新约束

可以通过`setNeedsUpdateConstraints`和`layoutIfNeeded`两个方法来刷新约束，使得UIView重新布局。

# Masonry框架

Masonry是AutoLayout的一个第三方类库，用链式语法封装了冗长的AutoLayout代码

``` objc
- (NSArray *)mas_makeConstraints:(void(^)(MASConstraintMaker *make))block;
- (NSArray *)mas_updateConstraints:(void(^)(MASConstraintMaker *make))block;
- (NSArray *)mas_remakeConstraints:(void(^)(MASConstraintMaker *make))block;
/*
mas_makeConstraints 只负责新增约束 Autolayout不能同时存在两条针对于同一对象的约束 否则会报错 
mas_updateConstraints 针对上面的情况 会更新在block中出现的约束 不会导致出现两个相同约束的情况
mas_remakeConstraints 则会清除之前的所有约束 仅保留最新的约束
三种函数善加利用 就可以应对各种情况了
*/
```

常用属性

``` objc
//乘以
- (MASConstraint * (^)(CGFloat multiplier))multipliedBy;
//除以
- (MASConstraint * (^)(CGFloat divider))dividedBy;
//优先级
- (MASConstraint * (^)(MASLayoutPriority priority))priority;
```

# Visual Format Language

VFL(Visual Format Language)允许你使用一种ASCII格式的字符串定义约束. 通过一行代码, 你可以在水平或者垂直方向上指定多个约束, 这跟一次只能创建一个约束相比会节省大量的代码量。

- 创建水平和垂直约束
- 在VFL中使用`views`
- 在VFL中使用`metrics`
- 使用layout options布局界面
- 使用layout guides

# 参考链接

https://www.jianshu.com/p/7b61998c6d8f