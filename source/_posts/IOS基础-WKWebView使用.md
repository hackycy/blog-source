---
title: IOS基础 - WKWebView使用
date: 2019-02-28 15:25:01
keywords:
    - Ios
    - WKWebView
    - WebKit
tags:
    - Ios
categories:
    - Ios
---

# 简介

WKWebView是显示交互式Web内容的对象，例如用于应用内浏览器。

<!-- more -->

**WKWebView对比UIWebView的优势**

>1.WKWebView的内存开销要比UIWebView小很多
>2.拥有高达60FPS滚动刷新率及内置手势
>3.支持了更多的HTML5特性
>4.html页面和WKWebView交互更方便
>5.Safari相同的JavaScript引擎
>6.提供常用的属性，如加载网页进度的属性estimatedProgress

# [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview)

## 类摘要

``` swift
class WKWebView : UIView
```

>  从iOS 8.0和OS X 10.10开始，使用WKWebView将Web内容添加到您的应用程序。不要使用UIWebView或WebView。

## 类方法

### 确定WebKit是否可以加载内容

``` swift
class func handlesURLScheme(String) -> Bool
//返回WebKit是否本机支持使用特定URL方案加载资源。
```

### 初始化Web视图

``` swift
var configuration: WKWebViewConfiguration
//用于初始化Web视图的配置副本。

init(frame: CGRect, configuration: WKWebViewConfiguration)
//返回使用指定框架和配置初始化的Web视图。

init?(coder: NSCoder)
```

### 检查视图信息

``` swift
var scrollView: UIScrollView
//与Web视图关联的滚动视图。

var title: String?
//当前页面标题。

var url: URL?
//当前活动网址。

var customUserAgent: String?
//自定义用户代理字符串。

var serverTrust: SecTrust?
//当前已提交导航的SecTrustRef对象。

var certificateChain: [Any]
//形成当前已提交导航的证书链的对象数组。 -- 弃用
```

### 设置代理

``` swift
var navigationDelegate: WKNavigationDelegate?
//Web视图的导航委托。

var uiDelegate: WKUIDelegate?
//Web视图的用户界面委托。
```

### 加载内容

``` swift
var estimatedProgress: Double
//估计当前导航的加载比例。

var hasOnlySecureContent: Bool
//一个布尔值，指示页面上的所有资源是否已通过安全加密的连接加载。

func loadHTMLString(String, baseURL: URL?) -> WKNavigation?
//设置网页内容和基本URL。

var isLoading: Bool
//一个布尔值，指示视图当前是否正在加载内容。

func reload() -> WKNavigation?
//重新加载当前页面。

func reload(Any?)
//重新加载当前页面。

func reloadFromOrigin() -> WKNavigation?
//重新加载当前页面，如果可能，使用缓存验证条件执行端到端重新验证。

func reloadFromOrigin(Any?)
//重新加载当前页面，如果可能，使用缓存验证条件执行端到端重新验证。

func stopLoading()
//停止加载当前页面上的所有资源。

func stopLoading(Any?)
//停止加载当前页面上的所有资源。

func load(Data, mimeType: String, characterEncodingName: String, baseURL: URL) -> WKNavigation?
//设置网页内容和基本URL。

func loadFileURL(URL, allowingReadAccessTo: URL) -> WKNavigation?
//导航到文件系统上请求的文件URL
```

### 缩放内容

``` swift
var allowsMagnification: Bool
//一个布尔值，指示放大手势是否会更改Web视图的放大率。

var magnification: CGFloat
//页面内容当前缩放的因子。

func setMagnification(CGFloat, centeredAt: CGPoint)
//按指定因子缩放页面内容，并将结果集中在指定点上。
```

### 导航

``` swift
var allowsBackForwardNavigationGestures: Bool
//一个布尔值，指示水平滑动手势是否会触发后退列表导航。

var backForwardList: WKBackForwardList
//Web视图的后退列表。

var canGoBack: Bool
//一个布尔值，指示后向列表中是否有可以导航到的后退项。

var canGoForward: Bool
//一个布尔值，指示后向列表中是否存在可以导航到的前向项。

var allowsLinkPreview: Bool
//一个布尔值，用于确定按下链接是否显示链接目标的预览。

func goBack() -> WKNavigation?
//导航到后退列表中的后退项目。

func goBack(Any?)
//导航到后退列表中的后退项目。

func goForward() -> WKNavigation?
//导航到后退列表中的前进项目。

func goForward(Any?)
//导航到后退列表中的前进项目。

func go(to: WKBackForwardListItem) -> WKNavigation?
//导航到后退列表中的项目并将其设置为当前项目。

func load(URLRequest) -> WKNavigation?
//导航到请求的URL。
```

### 执行JS

``` swift
func evaluateJavaScript(String, completionHandler: ((Any?, Error?) -> Void)?)
//JavaScript字符串。
```

### 拍摄快照

``` swift
func takeSnapshot(with: WKSnapshotConfiguration?, completionHandler: (UIImage?, Error?) -> Void)
//拍摄视图可见视口的快照。
```

## 简单使用

``` swift
import UIKit
import WebKit
class ViewController: UIViewController, WKUIDelegate {
    
    var webView: WKWebView!
    
    override func loadView() {
        let webConfiguration = WKWebViewConfiguration()
        webView = WKWebView(frame: .zero, configuration: webConfiguration)
        webView.uiDelegate = self
        view = webView
    }
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let myURL = URL(string:"https://www.apple.com")
        let myRequest = URLRequest(url: myURL!)
      	//加载URL
        webView.load(myRequest)
}}
```

要允许用户在网页历史记录中前后移动，请使用和方法作为按钮操作。当用户无法向某个方向移动时，请使用和属性禁用按钮。[`goBack()`](https://developer.apple.com/documentation/webkit/wkwebview/1414952-goback)、[`goForward()`](https://developer.apple.com/documentation/webkit/wkwebview/1414993-goforward)、[`canGoBack`](https://developer.apple.com/documentation/webkit/wkwebview/1414966-cangoback)、[`canGoForward`](https://developer.apple.com/documentation/webkit/wkwebview/1414962-cangoforward)。

默认情况下，Web视图会自动将Web内容中显示的电话号码转换为电话链接。点击电话链接后，电话应用程序将启动并拨打该号码。若要关闭此默认行为，请使用不包含该标志的位域设置该属性。[`dataDetectorTypes`](https://developer.apple.com/documentation/webkit/wkwebviewconfiguration/1641937-datadetectortypes)、[`WKDataDetectorTypes`](https://developer.apple.com/documentation/webkit/wkdatadetectortypes)、[`phoneNumber`](https://developer.apple.com/documentation/webkit/wkdatadetectortypes/1641917-phonenumber)

您还可以使用以第一次在Web视图中显示的方式以编程方式设置Web内容的比例。此后，用户可以使用手势来改变比例。[`setMagnification(_:centeredAt:)`](https://developer.apple.com/documentation/webkit/wkwebview/1414996-setmagnification)

# [WKNavigationDelegate](https://developer.apple.com/documentation/webkit/wknavigationdelegate)

WKNavigationDelegate协议的方法可帮助您实现在Web视图接受，加载和完成导航请求的过程中触发的自定义行为。

## 协议方法

### 启动导航

``` swift
func webView(WKWebView, didCommit: WKNavigation!)
//在Web视图开始接收Web内容时调用。

func webView(WKWebView, didStartProvisionalNavigation: WKNavigation!)
//在Web视图中开始加载Web内容时调用。
```

### 响应服务器操作

``` swift
func webView(WKWebView, didCommit: WKNavigation!)
//在Web视图开始接收Web内容时调用。

func webView(WKWebView, didReceiveServerRedirectForProvisionalNavigation: WKNavigation!)
//在Web视图收到服务器重定向时调用。
```

###  身份验证

```swift
func webView(WKWebView, didReceive: URLAuthenticationChallenge, completionHandler: (URLSession.AuthChallengeDisposition, URLCredential?) -> Void)
//在Web视图需要响应身份验证质询时调用。
```

### 错误处理

``` swift
func webView(WKWebView, didFail: WKNavigation!, withError: Error)
//在导航期间发生错误时调用。

func webView(WKWebView, didFailProvisionalNavigation: WKNavigation!, withError: Error)
//在Web视图加载内容时发生错误时调用。
```

### 跟踪加载进度

``` swift
func webView(WKWebView, didFinish: WKNavigation!)
//导航完成时调用。

func webViewWebContentProcessDidTerminate(WKWebView)
//在Web视图的Web内容进程终止时调用。
```

### 允许导航

``` swift
func webView(WKWebView, decidePolicyFor: WKNavigationAction, decisionHandler: (WKNavigationActionPolicy) -> Void)
//决定是允许还是取消导航。

func webView(WKWebView, decidePolicyFor: WKNavigationResponse, decisionHandler: (WKNavigationResponsePolicy) -> Void)
//决定在知道响应后是允许还是取消导航。
```

### 导航政策

``` swift
enum WKNavigationActionPolicy
//从该方法传回决策处理程序的策略。
webView(_:decidePolicyFor:decisionHandler:)

enum WKNavigationResponsePolicy
//从该方法传回决策处理程序的策略。
webView(_:decidePolicyFor:decisionHandler:)
```

# [WKUIDelegate](https://developer.apple.com/documentation/webkit/wkuidelegate)

WKUIDelegate协议提供网页的呈现接口的方法，例如alert。

WKWebView委托实现此协议以控制新窗口的打开，增强用户单击元素时显示的默认菜单项的行为，以及执行其他与用户界面相关的任务。可以在处理JavaScript或其他插件内容时调用这些方法。默认Web视图实现假定每个Web视图有一个窗口，因此非传统用户界面可能实现用户界面委托。

## 协议方法

### 创建Web视图

``` swift
func webView(WKWebView, createWebViewWith: WKWebViewConfiguration, for: WKNavigationAction, windowFeatures: WKWindowFeatures) -> WKWebView?
//创建新的Web视图。
```

### 显示UI面板

``` swift
func webView(WKWebView, runJavaScriptAlertPanelWithMessage: String, initiatedByFrame: WKFrameInfo, completionHandler: () -> Void)
//显示JavaScript警报面板。

func webView(WKWebView, runJavaScriptConfirmPanelWithMessage: String, initiatedByFrame: WKFrameInfo, completionHandler: (Bool) -> Void)
//显示JavaScript确认面板。

func webView(WKWebView, runJavaScriptTextInputPanelWithPrompt: String, defaultText: String?, initiatedByFrame: WKFrameInfo, completionHandler: (String?) -> Void)
//显示JavaScript文本输入面板。
```

### 关闭Web视图

``` swift
func webViewDidClose(WKWebView)
//通知您的应用程序DOM窗口已成功关闭。
```

### 显示上传面板

``` swift
func webView(WKWebView, runOpenPanelWith: WKOpenPanelParameters, initiatedByFrame: WKFrameInfo, completionHandler: ([URL]?) -> Void)
//显示文件上传面板。
```

### 响应强制触摸操作

``` swift
func webView(WKWebView, shouldPreviewElement: WKPreviewElementInfo) -> Bool
//确定给定元素是否应显示预览。 -- 弃用
func webView(WKWebView, previewingViewControllerForElement: WKPreviewElementInfo, defaultActions: [WKPreviewActionItem]) -> UIViewController?
//当用户执行窥视动作时调用。 -- 弃用
func webView(WKWebView, commitPreviewingViewController: UIViewController)
//当用户在预览上执行弹出操作时调用。 -- 弃用
```

# [WKWebViewConfiguration](https://developer.apple.com/documentation/webkit/wkwebviewconfiguration)

用于初始化Web视图的属性集合。使用该类，您可以确定网页的呈现时间，处理媒体播放的方式，用户可以选择的项目的粒度以及许多其他选项。`WKWebViewConfiguration`仅在首次初始化Web视图时使用。创建后，您无法使用此类更改Web视图的配置。

## 类方法

### 配置新Web视图的属性

``` swift
var applicationNameForUserAgent: String?
//用户代理字符串中使用的应用程序的名称。

var preferences: WKPreferences
//Web视图要使用的首选项对象。

var processPool: WKProcessPool
//从中获取视图的Web内容过程的进程池。

var userContentController: WKUserContentController
//JS注入脚本到WebView

var websiteDataStore: WKWebsiteDataStore
//Web视图要使用的网站数据存储。
```

### 确定网页可伸缩性

``` swift
var ignoresViewportScaleLimits: Bool
//一个布尔值，用于确定对象是否应始终允许缩放网页。
```

### 设置渲染首选项

``` swift
var suppressesIncrementalRendering: Bool
//一个布尔值，指示Web视图是否在内容呈现完全加载到内存之前抑制内容呈现。
```

### 设置媒体播放首选项

``` swift
var allowsInlineMediaPlayback: Bool
//一个布尔值，指示HTML5视频是否内联播放或使用本机全屏控制器。

var allowsAirPlayForMediaPlayback: Bool
//一个布尔值，指示是否允许AirPlay。

var allowsPictureInPictureMediaPlayback: Bool
//一个布尔值，指示HTML5视频是否可以播放画中画。

var mediaTypesRequiringUserActionForPlayback: WKAudiovisualMediaTypes
//确定哪些媒体类型需要用户手势才能开始播放。

var mediaPlaybackAllowsAirPlay: Bool
//allowsAirPlayForMediaPlayback -- 弃用

var requiresUserActionForMediaPlayback: Bool
//一个布尔值，指示HTML5视频是否要求用户开始播放（true）或视频是否可以自动播放（false）。 -- 弃用
var mediaPlaybackRequiresUserAction: Bool
//mediaTypesRequiringUserActionForPlayback -- 弃用

struct WKAudiovisualMediaTypes
//需要用户手势才能开始播放的媒体类型
```

### 设置选择粒度

``` swift
var selectionGranularity: WKSelectionGranularity
//用户可以在Web视图中以交互方式选择内容的粒度级别。

enum WKSelectionGranularity
//用于以交互方式创建和修改选择的粒度。
```

### 选择用户界面方向性

``` swift
var userInterfaceDirectionPolicy: WKUserInterfaceDirectionPolicy
//用户界面元素的方向性。

enum WKUserInterfaceDirectionPolicy
//该策略用于确定Web视图中用户界面元素的方向性。
```

### 识别数据类型

``` swift
var dataDetectorTypes: WKDataDetectorTypes
//所需的数据检测类型。

struct WKDataDetectorTypes
//检测到的数据类型。
```

### 为新的URL方案添加处理程序

``` swift
func setURLSchemeHandler(WKURLSchemeHandler?, forURLScheme: String)
//为给定的URL方案添加URL方案处理程序对象。

func urlSchemeHandler(forURLScheme: String) -> WKURLSchemeHandler?
//返回给定URL方案的当前注册的方案处理程序
```

# [WKPreferences](https://developer.apple.com/documentation/webkit/wkpreferences)

`WKPreferences`对象封装用于web视图偏好设置。与Web视图关联的首选项对象由其Web视图配置指定。

## 类方法

### 设置渲染首选项

``` swift
var minimumFontSize: CGFloat
//最小字体大小。

var tabFocusesLinks: Bool
```

### 设置Java和JavaScript首选项

``` swift
var javaEnabled: Bool
//一个布尔值，指示是否启用了Java。 -- 弃用
var javaScriptCanOpenWindowsAutomatically: Bool
//一个布尔值，指示JavaScript是否可以打开窗口而无需用户交互。

var javaScriptEnabled: Bool
//一个布尔值，指示是否启用JavaScript。

var plugInsEnabled: Bool
//一个布尔值，指示是否启用了插件。 -- 弃用
```

# [WKProcessPool](https://developer.apple.com/documentation/webkit/wkprocesspool)

WKProcessPool对象表示Web内容进程的池。

与Web视图关联的进程池由其Web视图配置指定。每个Web视图都有自己的Web内容流程，直到达到实现定义的流程限制；之后，具有相同流程池的Web视图最终共享Web内容流程。

> 该类没有属性或它自己的方法

# [WKUserContentController](https://developer.apple.com/documentation/webkit/wkusercontentcontroller)

该对象为Javascript提供了注射用户脚本到WKWebVIew的方法。

## 类方法

### 添加消息处理程序

``` swift
func add(WKScriptMessageHandler, name: String)
//添加脚本消息处理程序。
```

### 添加和删除用户脚本

``` swift
func addUserScript(WKUserScript)
//添加用户脚本。

func removeAllUserScripts()
//删除所有关联的用户脚本。

func removeScriptMessageHandler(forName: String)
//删除脚本消息处理程序。

var userScripts: [WKUserScript]
//与用户内容控制器关联的用户脚本。
```

### 添加和删除内容规则

``` swift
func add(WKContentRuleList)
//添加内容规则列表。

func remove(WKContentRuleList)
//删除规则列表。

func removeAllContentRuleLists()
//删除所有规则列表。

class WKContentRuleList
//要应用于Web内容的已编译规则列表。

class WKContentRuleListStore
```

# WKWebView使用

上边的一些概念介绍完后，我们来使用一下

## 常用方法

![](oftenuse.png)

## 基本创建和配置

引入模块

``` swift
import Webkit
```

ViewController中实现`WKUIDelegate`、`WKNavigationDelegate`协议

``` swift
class WKWebViewController: UIViewController, WKUIDelegate, WKNavigationDelegate
```

设置WKWebView的配置项

``` swift
let config = WKWebViewConfiguration()
config.preferences = WKPreferences()
//IOS 下默认NO， Mac下为YES
config.preferences.javaScriptCanOpenWindowsAutomatically = false
//默认为YES
config.preferences.javaScriptEnabled = true
//默认为0
config.preferences.minimumFontSize = 10
//通过js与webview内容交互配置
config.userContentController = WKUserContentController()
// 在载入时就添加JS,页面通过调用showAlert函数即可
let script = WKUserScript(source: "function showAlert() { alert('在载入webview时通过Swift注入的JS方法'); }", injectionTime: .atDocumentStart, forMainFrameOnly: true) // 只添加到mainFrame中
config.userContentController.addUserScript(script)
```

创建WKWebVIew并遵守协议

``` swift
self.webview = WKWebView(frame: self.view.frame, configuration: configWebView())
self.webview.uiDelegate = self
self.webview.navigationDelegate = self
//开启滑动返回
self.webview.allowsBackForwardNavigationGestures = true
```

## WKWebVIew加载本地html

``` swift
let url = Bundle.main.url(forResource: "index", withExtension: "html")!
self.webview.loadFileURL(url, allowingReadAccessTo: Bundle.main.bundleURL)
```

> html文件放在项目目录即可。有文件夹时请更改路径

## 实现WKUIDelegate

在网页中一般都会含有alert、promt、confirm等的对话框，需要我们原生去编码实现。

先创建一个`index.html`放在项目目录下

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <button onclick="alert('这是按钮点击的alert')">alert</button>
    <button onclick="confirm('这是按钮点击的confirm')">confirm</button>
    <button onclick="prompt('这是按钮点击的promt', '默认文字')">promt</button>
    <a href="http://www.baidu.com" target="_blank">新标签</a>
</body>
</html>
```

新建一个类并继承`ViewController`，并遵循`WKUIDelegate`协议：

``` swift
class WKUIViewController: UIViewController, WKUIDelegate 
```

导入库，并添加webview到界面上，并加载本地index.html文件。

``` swift
let url = Bundle.main.url(forResource: "index", withExtension: "html")!
self.webview.loadFileURL(url, allowingReadAccessTo: Bundle.main.bundleURL)
```

预览一下界面：

![](uidelegatedemo.png)

可以发现点击是没有任何反应的，但是不实现WKUIDelegate协议或者实现了协议但不实现方法是不会报错的，但是实现了方法又不在方法体内调用回调函数是会报错的。

这里使用UIAlertController简单实现UI：

``` swift
//Alert
func webView(_ webView: WKWebView, runJavaScriptAlertPanelWithMessage message: String, initiatedByFrame frame: WKFrameInfo, completionHandler: @escaping () -> Void) {
  let alert = UIAlertController(title: "提示", message: message, preferredStyle: UIAlertController.Style.alert)
  let sure = UIAlertAction(title: "确定", style: UIAlertAction.Style.default) { (action: UIAlertAction) in
    completionHandler()
  }
  alert.addAction(sure)
  self.present(alert, animated: true)
}
    
//Confirm
func webView(_ webView: WKWebView, runJavaScriptConfirmPanelWithMessage message: String, initiatedByFrame frame: WKFrameInfo, completionHandler: @escaping (Bool) -> Void) {
  let alert = UIAlertController(title: "提示", message: message, preferredStyle: UIAlertController.Style.alert)
  let sure = UIAlertAction(title: "确定", style: UIAlertAction.Style.default) { (action: UIAlertAction) in
    completionHandler(true)
  }
  let cancel = UIAlertAction(title: "取消", style: UIAlertAction.Style.cancel) { (action: UIAlertAction) in
    completionHandler(false)
  }
  alert.addAction(sure)
  alert.addAction(cancel)
  self.present(alert, animated: true, completion: nil)
}
    
//Prompt
func webView(_ webView: WKWebView, runJavaScriptTextInputPanelWithPrompt prompt: String, defaultText: String?, initiatedByFrame frame: WKFrameInfo, completionHandler: @escaping (String?) -> Void) {
  let alert = UIAlertController(title: "提示", message: prompt, preferredStyle: UIAlertController.Style.alert)
  alert.addTextField { (field: UITextField) in
    field.text = defaultText
  }
  let cancel = UIAlertAction(title: "取消", style: UIAlertAction.Style.cancel) { (action: UIAlertAction) in
    completionHandler(nil)
  }
  let sure = UIAlertAction(title: "确定", style: UIAlertAction.Style.default) { (action: UIAlertAction) in
    completionHandler(alert.textFields?.first?.text)
  }
  alert.addAction(sure)
  alert.addAction(cancel)
  self.present(alert, animated: true, completion: nil)      
}

//页面是弹出窗口时
func webView(_ webView: WKWebView, createWebViewWith configuration: WKWebViewConfiguration, for navigationAction: WKNavigationAction, windowFeatures: WKWindowFeatures) -> WKWebView? {
  //_blank处理
  self.webview.load(navigationAction.request)
  return nil
}
```

实现效果：

![](uidelegatedemo2.png)

## 实现标题栏和加载进度条

WKWebView中有三个属性是支持KVO的。因此我们可以通过监听其值的变化来实现对应的功能，即`loading`、`title`、`estimatedProgress`三个属性。

新建一个类并继承`ViewController`，并遵循`WKUIDelegate`与`WKNavigationDelegate`协议：

```swift
class BrowserViewController: UIViewController, WKUIDelegate, WKNavigationDelegate 
```

初始化WKWebView方法

``` swift
var webview: WKWebView!

func initWebView() {
  self.webview = WKWebView(frame: self.view.frame, configuration: WKWebViewConfiguration())
  self.view.addSubview(self.webview)
  self.webview.load(URLRequest(url: URL(string: "https://objccn.io/")!))
  //kvo监听以实现标题栏和进度条
  self.webview.addObserver(self, forKeyPath: "estimatedProgress", options: NSKeyValueObservingOptions.new, context: nil)
  self.webview.addObserver(self, forKeyPath: "title", options: NSKeyValueObservingOptions.new, context: nil)
  //遵守协议
  self.webview.uiDelegate = self
  self.webview.navigationDelegate = self
  //开启滑动返回
  self.webview.allowsBackForwardNavigationGestures = true
}
```

初始化进度条方法

``` swift
func initProgress() {
  //添加进度条
  progress = UIProgressView(frame: CGRect(x: 0, y: 0, width: UIScreen.main.bounds.width, height: 2))
  progress.backgroundColor = UIColor.blue
  self.view.addSubview(self.progress)
}
```

并在ViewDidLoad方法中调用。重载监听方法

``` swift
//在监听方法中监听进度条和标题的变化
override func observeValue(forKeyPath keyPath: String?, of object: Any?, change: [NSKeyValueChangeKey : Any]?, context: UnsafeMutableRawPointer?) {
  if keyPath == "estimatedProgress" {
    self.progress.setProgress(Float(self.webview.estimatedProgress), animated: true)
  } else if keyPath == "title" {
    self.navigationController?.title = self.webview.title
  } else {
    super.observeValue(forKeyPath: keyPath, of: object, change: change, context: context)
  }
}
```

在实现WKNavigationDelegate的两个代理方法以实现进度条的隐藏和现实

``` swift
- (void)webView:(WKWebView *)webView didStartProvisionalNavigation:(WKNavigation *)navigation {
    [self.progressView setHidden:NO];
}

//导航完成时调用。
- (void)webView:(WKWebView *)webView didFinishNavigation:(WKNavigation *)navigation {
    self.progress.progress = 0.0;
  	[self.progressView setHidden:YES];
}
```

在页面消失后取消监听，否则可能会出现一些未知的异常，在ViewController生命周期的viewDidDisappear方法中实现：

``` swift
override func viewDidDisappear(_ animated: Bool) {
  super.viewDidDisappear(animated)
  self.webview.removeObserver(self, forKeyPath: "estimatedProgress")
  self.webview.removeObserver(self, forKeyPath: "title")
}
```

运行效果：

![](browserdemo.png)

## 应用内H5跳转第三方APP

应用内web视图页面都会有一些跳转第三方APP的需求，获取跳转APP Store。

这里有一个`redirect.html`页面，分别是跳转粉丝福利购、拨打电话、跳转微信APP Store。放在了项目根目录下：

``` html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>跳转</title>
    </head>
    <body>
        <a
            href="taobao://uland.taobao.com/coupon/edetail?e=cSq3k99USIAGQASttHIRqbFZJ9euuZYQylASqMukPydjTfHRi%2FIrN%2BlmuMDII2iZ%2FASFi097%2BCOSU6IuEVIxtk4aJXRcltkaWv9OAj9evKDahba4h8MrZ%2Bdth9k8bqqSHKTgBzHkoM7XTQC0vfau6E%2F9Zk7cDx8UPY2GSU4OeGe2s6x1NZzECw73BakMbfZC&traceId=0b01319f15597018122426441e&union_lens=lensId:0b015dd6_0b58_16b2578f429_586d&xId=jwddSujflRkngxB4Q9tRDr3m1VxxlYIIvkQWXrAl4fhWOjMBIOJ0GkUZr8KQHPXIRTVNgd51FScfpn3ZqARc4s&activityId=d236dbef811a47b69494c70b8b22012e">测试</a>
        <a href="tel://15622472425">打电话给客服</a>
        <a href="https://itunes.apple.com/cn/app/wechat/id414478124?mt=8">微信链接</a>
    </body>
</html>
```

创建WKWebView就不再多讲了，需要跳转第三方APP或者打开APP Store的链接就需要遵循`WKNavigationDelegate`协议，并实现`webView(_:decidePolicyFor:)`方法：

``` swift
func webView(_ webView: WKWebView, decidePolicyFor navigationAction: WKNavigationAction, decisionHandler: @escaping (WKNavigationActionPolicy) -> Void) {
  //判断scheme，http、https、file则正常在页面加载
  let scheme = navigationAction.request.url?.scheme
  if scheme == "http" || scheme == "https" || scheme == "file" {
    //允许导航
    decisionHandler(WKNavigationActionPolicy.allow)
  } else {
    //否则例如scheme为taobao、alipay等则根据第三方sdk在网页中配置url，这里是都可以跳转
    //取消导航，打开APP
    UIApplication.shared.openURL(navigationAction.request.url!)
    decisionHandler(WKNavigationActionPolicy.cancel)
  }
}
```

> 对于自定义的 `URL Scheme` 类型链接，如果不实现该方法，在 `WKWebView` 里直接点击则会报错：`Error Domain=NSURLErrorDomain Code=-1002 "unsupported URL"`。
>
> 如果需要具体判断到打开某些app只要具体判断scheme即可。
>
> 跳转APP Store的话因为app store的网页已经配置了Universal Link跳转，所以不需要额外的配置即可跳转。
>
> 注意：必需要在真机上测试。

# 仿微信WebView

运行效果：

![image](https://user-images.githubusercontent.com/26972260/83226604-eb2cfa00-a1b4-11ea-85b3-36d8909c4488.png)

实现代码：

``` swift
//
//  CommonWebViewController.swift
//  xianqi
//
//  Created by yzy on 2020/4/17.
//

import UIKit
import WebKit

class CommonWebViewController: UIViewController, WKUIDelegate, WKNavigationDelegate, UIScrollViewDelegate {
    
    /// 加载的链接
    var requestUrl: String? {
        didSet {
            self.webview.load(URLRequest(url: URL(string: self.requestUrl!)!))
        }
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        //四周均不延伸，以免出现遮挡
        self.edgesForExtendedLayout = []
        self.initWebView()
        self.initProgressView()
        self.setupToolbarItems()
        // 加载URL
        self.requestUrl = "https://www.baidu.com"
    }
    
    // MARK: toolbar
        
    lazy var goBackItem: UIBarButtonItem = {
        let item: UIBarButtonItem = UIBarButtonItem(image: UIImage(named: "icon_left_arrow"), style: .done, target: self, action: #selector(CommonWebViewController.goBack))
        item.tintColor = UIColor.black
        return item
    }()
    
    lazy var goForwardItem: UIBarButtonItem = {
        let item: UIBarButtonItem = UIBarButtonItem(image: UIImage(named: "icon_right_arrow"), style: .done, target: self, action: #selector(CommonWebViewController.goForword))
        item.tintColor = UIColor.black
        return item
    }()
    
    lazy var flexibleItem: UIBarButtonItem = {
        return UIBarButtonItem(barButtonSystemItem: .flexibleSpace, target: nil, action: nil)
    }()
    
    @objc func goBack() {
        if self.webview.canGoBack {
            webview.goBack()
        }
    }
    
    @objc func goForword() {
        if self.webview.canGoForward {
            webview.goForward()
        }
    }
    
    func setupToolbarItems() {
        self.navigationController?.toolbar.isTranslucent = false
        self.toolbarItems = [flexibleItem, goBackItem, flexibleItem, goForwardItem, flexibleItem]
    }
    
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        // 当Push进来时如果页面没有显示NavBar，则WebView手动显示
        if self.navigationController?.navigationBar.isHidden ?? false {
            self.navigationController?.setNavigationBarHidden(false, animated: true)
        }
    }
    
    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
    }
    
    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        hiddenToolbar()
    }
    
    func hiddenToolbar() {
        self.navigationController?.setToolbarHidden(true, animated: true)
    }
    
    func visibleToolbar() {
        if self.navigationController != nil {
            if self.navigationController!.isToolbarHidden {
                self.navigationController?.setToolbarHidden(false, animated: true)
                self.goForwardItem.isEnabled = self.webview.canGoForward
                self.goBackItem.isEnabled = self.webview.canGoBack
            }
        }
    }
    
    // MARK: webview相关
    
    lazy var webview: WKWebView = {
        let wv: WKWebView = WKWebView()
        // normal setting
        wv.uiDelegate = self
        wv.navigationDelegate = self
        return wv
    }()
    
    lazy var progressView: UIProgressView = {
        let view: UIProgressView = UIProgressView(progressViewStyle: .bar)
        view.trackTintColor = UIColor.gray
        view.progressTintColor = UIColor.green.withAlphaComponent(0.55)
        return view
    }()
    
    func initWebView() {
        // add view
        self.view.addSubview(self.webview)
        // add constraint
        self.webview.translatesAutoresizingMaskIntoConstraints = false
        if #available(iOS 11.0, *) {
            self.view.addConstraint(NSLayoutConstraint(item: self.webview, attribute: .top, relatedBy: .equal, toItem: self.view.safeAreaLayoutGuide, attribute: .top, multiplier: 1.0, constant: 0))
            self.view.addConstraint(NSLayoutConstraint(item: self.webview, attribute: .bottom, relatedBy: .equal, toItem: self.view.safeAreaLayoutGuide, attribute: .bottom, multiplier: 1.0, constant: 0))
        } else {
            // Fallback on earlier versions
            self.view.addConstraint(NSLayoutConstraint(item: self.webview, attribute: .top, relatedBy: .equal, toItem: self.view, attribute: .top, multiplier: 1.0, constant: 0))
            self.view.addConstraint(NSLayoutConstraint(item: self.webview, attribute: .bottom, relatedBy: .equal, toItem: self.view, attribute: .bottom, multiplier: 1.0, constant: 0))
        }
        self.view.addConstraint(NSLayoutConstraint(item: self.webview, attribute: .left, relatedBy: .equal, toItem: self.view, attribute: .left, multiplier: 1.0, constant: 0))
        self.view.addConstraint(NSLayoutConstraint(item: self.webview, attribute: .right, relatedBy: .equal, toItem: self.view, attribute: .right, multiplier: 1.0, constant: 0))
        // 监听进度事件
        self.webview.addObserver(self, forKeyPath: #keyPath(WKWebView.estimatedProgress), options: .new, context: nil)
        // 监听网页标题
        self.webview.addObserver(self, forKeyPath: #keyPath(WKWebView.title), options: .new, context: nil)
        // 监听前进后退
        self.webview.addObserver(self, forKeyPath: #keyPath(WKWebView.canGoForward), options: .new, context: nil)
        self.webview.addObserver(self, forKeyPath: #keyPath(WKWebView.canGoBack), options: .new, context: nil)
        // 滚动监听
        self.webview.scrollView.delegate = self
        // 设置UA
        self.webview.evaluateJavaScript("navigator.userAgent") { (oldAgent: Any?, error: Error?) in
            if error == nil {
                //oldAgent = [NSString stringWithFormat:@"Mozilla/5.0 (%@; CPU iPhone OS %@ like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Mobile/15E148", [[UIDevice currentDevice] model], [[[UIDevice currentDevice] systemVersion] stringByReplacingOccurrencesOfString:@"." withString:@"_"]];
                let uaStr: String = oldAgent as? String ?? "Mozilla/5.0 (\(UIDevice.current.model); CPU iPhone OS \(UIDevice.current.systemVersion) like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Mobile/15E148"
                // 保存至本地
                UserDefaults.standard.set(uaStr, forKey: "USER_AGENT")
                // 获取app信息
                let infoDic = Bundle.main.infoDictionary
                // 获取App的版本号
                let appVersion = infoDic?["CFBundleShortVersionString"]
                // 获取App的build版本
                let appBuildVersion = infoDic?["CFBundleVersion"]
                // 真实拼接 UA
                let realUAStr = "\(uaStr)#xianqi#\(appVersion ?? 0)#\(appBuildVersion ?? 0)"
                self.webview.customUserAgent = realUAStr
            }
            
        }
    }
    
    // 手势拖动时控制Toolbar显示和隐藏 
    func scrollViewDidScroll(_ scrollView: UIScrollView) {
        if self.webview.backForwardList.backList.count <= 0 && self.webview.backForwardList.forwardList.count <= 0 {
            return
        }
        let vel = scrollView.panGestureRecognizer.velocity(in: scrollView)
        if vel.y < -8 {
            // 向上拖动
            hiddenToolbar()
        } else if vel.y > 8 {
            // 向下拖动
            visibleToolbar()
        }
    }
    
    /// 监听
    /// - Parameters:
    ///   - keyPath: keyPath description
    ///   - object: object description
    ///   - change: change description
    ///   - context: context description
    override func observeValue(forKeyPath keyPath: String?, of object: Any?, change: [NSKeyValueChangeKey : Any]?, context: UnsafeMutableRawPointer?) {
        if keyPath == #keyPath(WKWebView.estimatedProgress) {
            self.showProgressView(progress: Float(self.webview.estimatedProgress))
        } else if keyPath == #keyPath(WKWebView.title) {
            self.observeWebTitle(title: self.webview.title)
            // 控制显示Toolbar
            if self.webview.backForwardList.backList.count > 0 || self.webview.backForwardList.forwardList.count > 0 {
                visibleToolbar()
            }
        } else if keyPath == #keyPath(WKWebView.canGoForward) {
            self.goForwardItem.isEnabled = self.webview.canGoForward
        } else if keyPath == #keyPath(WKWebView.canGoBack) {
            self.goBackItem.isEnabled = self.webview.canGoBack
        } else {
            return super.observeValue(forKeyPath: keyPath, of: object, change: change, context: context)
        }
    }
    
    /// 用于方便子类重写
    /// - Parameter title: webview的标题
    func observeWebTitle(title: String?) {
        self.navigationItem.title = title
    }
    
    func initProgressView() {
        // layout
        self.view.addSubview(self.progressView)
        // add constraint
        self.progressView.translatesAutoresizingMaskIntoConstraints = false
        if #available(iOS 11.0, *) {
            self.view.addConstraint(NSLayoutConstraint(item: self.progressView, attribute: .top, relatedBy: .equal, toItem: self.view.safeAreaLayoutGuide, attribute: .top, multiplier: 1.0, constant: 0))
        } else {
            // Fallback on earlier versions
            self.view.addConstraint(NSLayoutConstraint(item: self.progressView, attribute: .top, relatedBy: .equal, toItem: self.view, attribute: .top, multiplier: 1.0, constant: 0))
        }
        // 设置宽度
        self.view.addConstraint(NSLayoutConstraint(item: self.progressView, attribute: .left, relatedBy: .equal, toItem: self.view, attribute: .left, multiplier: 1.0, constant: 0))
        self.view.addConstraint(NSLayoutConstraint(item: self.progressView, attribute: .right, relatedBy: .equal, toItem: self.view, attribute: .right, multiplier: 1.0, constant: 0))
        // 设置高度
        self.progressView.addConstraint(NSLayoutConstraint(item: self.progressView, attribute: .height, relatedBy: .equal, toItem: nil, attribute: .height, multiplier: 1.0, constant: 2))
    }
    
    func showProgressView(progress: Float) {
        if self.progressView.isHidden {
            self.progressView.isHidden = false
        }
        self.progressView.setProgress(progress, animated: true)
        if progress == 1.0 {
            self.hideProgressView()
        }
    }
    
    func hideProgressView() {
        self.progressView.isHidden = true
        self.progressView.setProgress(0.1, animated: false)
    }
    
    // MARK: WKUIDelegate
    
    func webView(_ webView: WKWebView, runJavaScriptAlertPanelWithMessage message: String, initiatedByFrame frame: WKFrameInfo, completionHandler: @escaping () -> Void) {
        let alert = UIAlertController(title: "提示", message: message, preferredStyle: UIAlertController.Style.alert)
        let sure = UIAlertAction(title: "确定", style: UIAlertAction.Style.default) { (action: UIAlertAction) in
          completionHandler()
        }
        alert.addAction(sure)
        self.present(alert, animated: true)
    }
    
    // MARK: WKNavigationDelegate

    func webView(_ webView: WKWebView, didFinish navigation: WKNavigation!) {
        self.hideProgressView()
    }
    
    func webView(_ webView: WKWebView, decidePolicyFor navigationAction: WKNavigationAction, decisionHandler: @escaping (WKNavigationActionPolicy) -> Void) {
        let scheme = navigationAction.request.url?.scheme
        // about 用于解决LayUI框架显示问题
        if scheme == "http" || scheme == "https" || scheme == "file" || scheme == "about" {
            decisionHandler(.allow)
            return
        }
        //否则例如scheme为taobao、alipay等则根据第三方sdk在网页中配置url，这里是都可以跳转
        if UIApplication.shared.canOpenURL(navigationAction.request.url!) {
            //取消导航，打开APP
            UIApplication.shared.openURL(navigationAction.request.url!)
            // 取消
            decisionHandler(.cancel)
            return
        }
        decisionHandler(.allow)
    }
    
    deinit {
        self.webview.removeObserver(self, forKeyPath: "estimatedProgress")
        self.webview.removeObserver(self, forKeyPath: "title")
        self.webview.removeObserver(self, forKeyPath: "url")
    }
    
}
```

> 包括了设置UA的方式，至于左侧的关闭按钮和右侧的更多按钮则可以自己实现美化。这里不多实现了。

# 资料参考

https://developer.apple.com/documentation/webkit/wkwebview

https://kangzubin.com/wkwebview-link/

https://www.jianshu.com/p/bf2008d80e2d

https://blog.csdn.net/kmonarch/article/details/83585795

https://www.jianshu.com/p/0f825df61037