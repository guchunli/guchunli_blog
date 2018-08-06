---
title: OC与JS交互的几种方法
date: 2017-07-04 11:59:35
categories: 笔记
tags: [OC,JS]
toc: true
---

OC与JS交互的几种方法
1.JavaScriptCore
2.WebViewJavascriptBridge
3.WKWebview
<!--more-->

# JavaScriptCore
## 通过JSContext实现OC与JS的交互
### 1.在webViewDidFinishLoad方法中创建JSContext对象,获取该UIWebview的javascript执行环境
```
- (void)webViewDidFinishLoad:(UIWebView *)webView {

    //通过当前webView的键获取到jscontext
    self.jsContext = [webView valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
    //以后JS就可以通过 OCModel 调用OC方法
    self.jsContext[@"OCModel"] = self;
    self.jsContext.exceptionHandler = ^(JSContext *context, JSValue *exceptionValue) {
        context.exception = exceptionValue;
        NSLog(@"异常信息：%@", exceptionValue);
    };
};
```

### 2.OC调用JS方法并传递一个参数
JS方法：
```
function getImg(path){
    //document.getElementById("test").src=path;
}
```

OC通过`evaluateScript`调用JS方法：
```
NSString *alertJS=[NSString stringWithFormat:@"getImg('%@')",@"abc"]; //准备执行的js代码
[self.jsContext evaluateScript:alertJS];
```

### 3.JS调用OC方法并传递一个参数
OC方法：
```
- (void)getImg:(NSString *)source{
    NSLog(@"%@",source);
}
```

JS通过`OCModel`调用OC方法：
```
function getGalleryImg(){
    OCModel.getImg('gallery');
}
```

## 通过request的URL实现交互
JS代码:
```
<script>
//触发该方法，调用OC中webview的`shouldStartLoadWithRequest`方法实现与OC交互
function goCamera(){
    window.location.href="js-call://camera/cameraCallback";
}
//OC回调的JS方法
function cameraCallback(data) {
    //获取到OC传递过来的参数
    alert(data);
}
</script>
```

OC代码:
```
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType
{
    NSString *requestString = [[request URL] absoluteString];
    //jS协议头
    NSString *protocol = @"js-call://";
    //请求的字符串符合协议头
    if ([requestString hasPrefix:protocol]) {
        //从协议头后的位置截取字符串到最后
        NSString *requestContent = [requestString substringFromIndex:[protocol length]];
        //将/分隔的字符串转换成数组
        NSArray *vals = [requestContent componentsSeparatedByString:@"/"];
        if ([[vals objectAtIndex:0] isEqualToString:@"camera"]) {
            //获取到回调方法：cameraCallback
            callback = [vals objectAtIndex:1];
            //OC回调JS方法，并传递一个参数
            [_webView stringByEvaluatingJavaScriptFromString:[NSString stringWithFormat:@"%@('%@');", callback, @"abc"]];
        } 
        else {
            [webView stringByEvaluatingJavaScriptFromString:@"alert('未定义');"];
        }
        return NO;
    }
    return YES;
}
```

# WebViewJavascriptBridge
## 1.通过cocoapods或手动导入WebViewJavascriptBridge框架
## 2.OC端:创建webview,与bridge对象建立联系
```
// 开启日志
[WebViewJavascriptBridge enableLogging];

// 指定webview，建立JS与OjbC的沟通桥梁
self.bridge = [WebViewJavascriptBridge bridgeForWebView:webView];
[self.bridge setWebViewDelegate:self];
```

## 3.JS端：创建setupWebViewJavascriptBridge
```
<script>
window.onerror = function(err) {
log('window.onerror: ' + err)
}

/*这段代码是固定的，必须要放到js中*/
function setupWebViewJavascriptBridge(callback) {
if (window.WebViewJavascriptBridge) { return callback(WebViewJavascriptBridge); }
if (window.WVJBCallbacks) { return window.WVJBCallbacks.push(callback); }
window.WVJBCallbacks = [callback];
var WVJBIframe = document.createElement('iframe');
WVJBIframe.style.display = 'none';
WVJBIframe.src = 'wvjbscheme://__BRIDGE_LOADED__';
document.documentElement.appendChild(WVJBIframe);
setTimeout(function() { document.documentElement.removeChild(WVJBIframe) }, 0)
}

/*与OC交互的所有JS方法都要放在此处注册，才能调用通过JS调用OC方法或者让OC调用这里的JS方法*/
setupWebViewJavascriptBridge(function(bridge) {

    //在这注册OC调用JS的方法，可以接收OC传过来的参数 data ，还可以通过 responseCallback 回调OC
    /*
    bridge.registerHandler('JSFunction', function(data, responseCallback) {
        responseCallback({'': ''})
    }
    */

    //这里可以调用OC方法，OC端在处理完成后，反馈给JS，这样写是在载入页面完成时就先调用
    document.getElementById('btn').onclick = function (e) {
    bridge.callHandler('OCFunction', {'js': 'abc'}, function(response) {
        alert(response)
    })
}

</script>
```

## 4.JS调用OC方法
bridge通过`registerHandler`注册提供给JS调用的方法
OC端通过responseCallback回调JS，JS就可以得到所需要的数据
OC方法：
```
[self.bridge registerHandler:@"JSCallOC" handler:^(id data, WVJBResponseCallback responseCallback) {
    NSLog(@"%@", data);     //js-oc,abc
    if (responseCallback) {
        // 回调给JS
        responseCallback(@{@"oc-js": @"123"});
    }
}];
```

JS调用OC方法：
```
bridge.callHandler('JSCallOC', {'js-oc': 'abc'}, function(responseData) {
    alert(responseData)     //oc-js,123
})
```

## 5.OC调用JS方法
JS方法：
```
/*JS给ObjC提供公开的API，在ObjC端可以手动调用JS的这个API。接收ObjC传过来的参数，且可以回调ObjC*/
bridge.registerHandler('OCCallJS', function(data, responseCallback) {
    alert(data)     //oc-js,123
    responseCallback({'a': '1', 'b': '2'})
})
```

OC调用JS方法：
```
[self.bridge callHandler:@"OCCallJS" data:@{@"oc-js": @"123"} responseCallback:^(id responseData) {
    NSLog(@"%@", responseData);     //'a': '1', 'b': '2'
}];
```

# WKWebview
## 1.添加JS交互方法
```
//添加JS交互方法
-(void)setupJSmethod{

// 注入JS对象名称AppModel，当JS通过AppModel来调用时，
// 我们可以在WKScriptMessageHandler代理中接收到
[self.userContentController addScriptMessageHandler:self name:@"AppModel"];
[self.userContentController addScriptMessageHandler:self name:@"userLogin"];
}
```

## 2.创建WKWebview
```
WKWebViewConfiguration *config = [[WKWebViewConfiguration alloc] init];
/*
NSString *js = @"I am JS Code";
//初始化WKUserScript对象
//根据生成的WKUserScript对象，初始化WKWebViewConfiguration
//WKUserScriptInjectionTimeAtDocumentEnd为网页加载完成时注入
WKUserScript *script = [[WKUserScript alloc] initWithSource:js injectionTime:WKUserScriptInjectionTimeAtDocumentEnd forMainFrameOnly:YES];
[config.userContentController addUserScript:script];
*/
// 注入JS对象名称AppModel，当JS通过AppModel来调用时，
// 我们可以在WKScriptMessageHandler代理中接收到
[config.userContentController addScriptMessageHandler:self name:@"AppModel"];


self.webView = [[WKWebView alloc] initWithFrame:self.view.bounds configuration:config];
// 导航代理
self.webView.navigationDelegate = self;
// 与webview UI交互代理
self.webView.UIDelegate = self;
[self.view addSubview:self.webView];

NSURL *path = [[NSBundle mainBundle] URLForResource:@"demo" withExtension:@"html"];
[self.webView loadRequest:[NSURLRequest requestWithURL:path]];
```

## 3.添加WKWebView属性的监听
```
// 添加KVO监听
[self.webView addObserver:self
forKeyPath:@"loading"
options:NSKeyValueObservingOptionNew
context:nil];
[self.webView addObserver:self
forKeyPath:@"title"
options:NSKeyValueObservingOptionNew
context:nil];
[self.webView addObserver:self
forKeyPath:@"estimatedProgress"
options:NSKeyValueObservingOptionNew
context:nil];


#pragma mark - KVO
- (void)observeValueForKeyPath:(NSString *)keyPath
ofObject:(id)object
change:(NSDictionary<NSString *,id> *)change
context:(void *)context {
if ([keyPath isEqualToString:@"loading"]) {
NSLog(@"loading");
} else if ([keyPath isEqualToString:@"title"]) {
self.title = self.webView.title;
} else if ([keyPath isEqualToString:@"estimatedProgress"]) {
NSLog(@"progress: %f", self.webView.estimatedProgress);
//        self.progressView.progress = self.webView.estimatedProgress;
}

// 加载完成
//    if (!self.webView.loading) {
//        // 手动调用JS代码
//        // 每次页面完成都弹出来，大家可以在测试时再打开
//        NSString *js = @"callJsAlert()";
//        [self.webView evaluateJavaScript:js completionHandler:^(id _Nullable response, NSError * _Nullable error) {
//            NSLog(@"response: %@ error: %@", response, error);
//            NSLog(@"call js alert by native");
//        }];
//
//        [UIView animateWithDuration:0.5 animations:^{
//            self.progressView.alpha = 0;
//        }];
//    }
}
```

## 4.WKScriptMessageHandler
```
#pragma mark - WKScriptMessageHandler
//JS调用iOS
- (void)userContentController:(WKUserContentController *)userContentController
didReceiveScriptMessage:(WKScriptMessage *)message {
if ([message.name isEqualToString:@"AppModel"]) {
// 打印所传过来的参数，只支持NSNumber, NSString, NSDate, NSArray,
// NSDictionary, and NSNull类型
NSLog(@"%@", message.body);
}else if ([message.name isEqualToString:@"userLogin"]){
NSLog(@"%@", message.body);

// 每次页面完成都弹出来，大家可以在测试时再打开
NSString *js = [NSString stringWithFormat:@"callJsUserName('%@')",message.body[@"body"]];
[self.webView evaluateJavaScript:js completionHandler:^(id _Nullable response, NSError * _Nullable error) {
NSLog(@"response: %@ error: %@", response, error);
NSLog(@"call js username by native");
}];
}
}
```

## 5.WKUIDelegate
```#pragma mark - WKUIDelegate
- (void)webViewDidClose:(WKWebView *)webView {
NSLog(@"%s", __FUNCTION__);
}

// 在JS端调用alert函数时，会触发此代理方法。
// JS端调用alert时所传的数据可以通过message拿到
// 在原生得到结果后，需要回调JS，是通过completionHandler回调
- (void)webView:(WKWebView *)webView runJavaScriptAlertPanelWithMessage:(NSString *)message initiatedByFrame:(WKFrameInfo *)frame completionHandler:(void (^)(void))completionHandler {
NSLog(@"%s", __FUNCTION__);
UIAlertController *alert = [UIAlertController alertControllerWithTitle:@"alert" message:@"JS调用alert" preferredStyle:UIAlertControllerStyleAlert];
[alert addAction:[UIAlertAction actionWithTitle:@"确定" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {
completionHandler();
}]];

[self presentViewController:alert animated:YES completion:NULL];
NSLog(@"%@", message);
}

// JS端调用confirm函数时，会触发此方法
// 通过message可以拿到JS端所传的数据
// 在iOS端显示原生alert得到YES/NO后
// 通过completionHandler回调给JS端
- (void)webView:(WKWebView *)webView runJavaScriptConfirmPanelWithMessage:(NSString *)message initiatedByFrame:(WKFrameInfo *)frame completionHandler:(void (^)(BOOL result))completionHandler {
NSLog(@"%s", __FUNCTION__);

UIAlertController *alert = [UIAlertController alertControllerWithTitle:@"confirm" message:@"JS调用confirm" preferredStyle:UIAlertControllerStyleAlert];
[alert addAction:[UIAlertAction actionWithTitle:@"确定" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {
completionHandler(YES);
}]];
[alert addAction:[UIAlertAction actionWithTitle:@"取消" style:UIAlertActionStyleCancel handler:^(UIAlertAction * _Nonnull action) {
completionHandler(NO);
}]];
[self presentViewController:alert animated:YES completion:NULL];

NSLog(@"%@", message);
}

// JS端调用prompt函数时，会触发此方法
// 要求输入一段文本
// 在原生输入得到文本内容后，通过completionHandler回调给JS
- (void)webView:(WKWebView *)webView runJavaScriptTextInputPanelWithPrompt:(NSString *)prompt defaultText:(nullable NSString *)defaultText initiatedByFrame:(WKFrameInfo *)frame completionHandler:(void (^)(NSString * __nullable result))completionHandler {
NSLog(@"%s", __FUNCTION__);

NSLog(@"%@", prompt);
UIAlertController *alert = [UIAlertController alertControllerWithTitle:@"textinput" message:@"JS调用输入框" preferredStyle:UIAlertControllerStyleAlert];
[alert addTextFieldWithConfigurationHandler:^(UITextField * _Nonnull textField) {
textField.textColor = [UIColor redColor];
}];

[alert addAction:[UIAlertAction actionWithTitle:@"确定" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {
completionHandler([[alert.textFields lastObject] text]);
}]];

[self presentViewController:alert animated:YES completion:NULL];
}
```

## 6.WKNavigationDelegate
```
#pragma mark - WKNavigationDelegate
// 请求开始前，会先调用此代理方法
// 与UIWebView的
// - (BOOL)webView:(UIWebView *)webView
// shouldStartLoadWithRequest:(NSURLRequest *)request
// navigationType:(UIWebViewNavigationType)navigationType;
// 类型，在请求先判断能不能跳转（请求）
- (void)webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction decisionHandler:(void (^)(WKNavigationActionPolicy))decisionHandler {
NSString *hostname = navigationAction.request.URL.host.lowercaseString;
if (navigationAction.navigationType == WKNavigationTypeLinkActivated
&& ![hostname containsString:@".baidu.com"]) {
// 对于跨域，需要手动跳转
[[UIApplication sharedApplication] openURL:navigationAction.request.URL];

// 不允许web内跳转
decisionHandler(WKNavigationActionPolicyCancel);
} else {
self.progressView.alpha = 1.0;
decisionHandler(WKNavigationActionPolicyAllow);
}

NSLog(@"%s", __FUNCTION__);
}

// 在响应完成时，会回调此方法
// 如果设置为不允许响应，web内容就不会传过来
- (void)webView:(WKWebView *)webView decidePolicyForNavigationResponse:(WKNavigationResponse *)navigationResponse decisionHandler:(void (^)(WKNavigationResponsePolicy))decisionHandler {
decisionHandler(WKNavigationResponsePolicyAllow);
NSLog(@"%s", __FUNCTION__);
}

// 开始导航跳转时会回调
- (void)webView:(WKWebView *)webView didStartProvisionalNavigation:(null_unspecified WKNavigation *)navigation {
NSLog(@"%s", __FUNCTION__);
}

// 接收到重定向时会回调
- (void)webView:(WKWebView *)webView didReceiveServerRedirectForProvisionalNavigation:(null_unspecified WKNavigation *)navigation {
NSLog(@"%s", __FUNCTION__);
}

// 导航失败时会回调
- (void)webView:(WKWebView *)webView didFailProvisionalNavigation:(null_unspecified WKNavigation *)navigation withError:(NSError *)error {
NSLog(@"%s", __FUNCTION__);
}

// 页面内容到达main frame时回调
- (void)webView:(WKWebView *)webView didCommitNavigation:(null_unspecified WKNavigation *)navigation {
NSLog(@"%s", __FUNCTION__);
}

// 导航完成时，会回调（也就是页面载入完成了）
- (void)webView:(WKWebView *)webView didFinishNavigation:(null_unspecified WKNavigation *)navigation {
NSLog(@"%s", __FUNCTION__);
}

// 导航失败时会回调
- (void)webView:(WKWebView *)webView didFailNavigation:(null_unspecified WKNavigation *)navigation withError:(NSError *)error {

}

// 对于HTTPS的都会触发此代理，如果不要求验证，传默认就行
// 如果需要证书验证，与使用AFN进行HTTPS证书验证是一样的
- (void)webView:(WKWebView *)webView didReceiveAuthenticationChallenge:(NSURLAuthenticationChallenge *)challenge completionHandler:(void (^)(NSURLSessionAuthChallengeDisposition disposition, NSURLCredential *__nullable credential))completionHandler {
NSLog(@"%s", __FUNCTION__);
completionHandler(NSURLSessionAuthChallengePerformDefaultHandling, nil);
}

// 9.0才能使用，web内容处理中断时会触发
- (void)webViewWebContentProcessDidTerminate:(WKWebView *)webView {
NSLog(@"%s", __FUNCTION__);
}
```

## 7.dealloc
```
- (void)dealloc {
[[_webView configuration].userContentController removeScriptMessageHandlerForName:@"方法名"];
}
```

## 8.JS端代码
```
<!DOCTYPE html>
<html>
<head>
<title>iOS and Js</title>
<style type="text/css">
* {
font-size: 40px;
}
</style>
</head>

<body>

<div style="margin-top: 100px">
<h1>Test how to use objective-c call js</h1><br/>
<div><input type="button" value="userLogin" onclick="callJsUserLogin()"></div>
<br/>
<div><input type="button" value="call js alert" onclick="callJsAlert()"></div>
<br/>
<div><input type="button" value="Call js confirm" onclick="callJsConfirm()"></div><br/>
</div>
<br/>
<div>
<div><input type="button" value="Call Js prompt " onclick="callJsInput()"></div><br/>
<div>Click me here: <a href="https://www.baidu.com">Jump to Baidu</a></div>
</div>

<br/>
<div id="SwiftDiv">
<span id="jsParamFuncSpan" style="color: red; font-size: 50px;"></span>
</div>

<script type="text/javascript">
function callJsAlert() {
alert('Objective-C call js to show alert');

window.webkit.messageHandlers.AppModel.postMessage({body: 'call js alert in js'});
}

function callJsConfirm() {
if (confirm('confirm', 'Objective-C call js to show confirm')) {
document.getElementById('jsParamFuncSpan').innerHTML
= 'true';
} else {
document.getElementById('jsParamFuncSpan').innerHTML
= 'false';
}

// AppModel是我们所注入的对象
window.webkit.messageHandlers.AppModel.postMessage({body: 'call js confirm in js'});
}

function callJsInput() {
var response = prompt('Hello', 'Please input your name:');
document.getElementById('jsParamFuncSpan').innerHTML = response;

// AppModel是我们所注入的对象
window.webkit.messageHandlers.AppModel.postMessage({body: response});
}
function callJsUserLogin(){
window.webkit.messageHandlers.userLogin.postMessage({body: 'xiaoming'});
}
function callJsUserName(username){
document.getElementById('jsParamFuncSpan').innerHTML
= username;
}
</script>
</body>
</html>
```

## 9.总结
* JS调用OC方法：
JS端：
```
window.webkit.messageHandlers.userLogin.postMessage({body: 'xiaoming'});
```

OC端：
```
- (void)userContentController:(WKUserContentController *)userContentController
didReceiveScriptMessage:(WKScriptMessage *)message {
    if ([message.name isEqualToString:@"userLogin"]){
        NSLog(@"%@", message.body);
    }
    //else if ...
}
```

* OC调用JS方法：
OC端：
```
NSString *username = @"xiaoming";
NSString *js = [NSString stringWithFormat:@"callJsUserName('%@')",username];
[self.webView evaluateJavaScript:js completionHandler:^(id _Nullable response, NSError * _Nullable error) {
NSLog(@"response: %@ error: %@", response, error);
NSLog(@"call js username by native");
}];
```

JS端：
```
function callJsUserName(username){
document.getElementById('jsParamFuncSpan').innerHTML
= username;
}
```


## 清除webview缓存
```
//清除cookies
NSHTTPCookie *cookie;
NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
for (cookie in [storage cookies]) {
[storage deleteCookie:cookie];
}

NSHTTPCookie *cookie = [NSHTTPCookie cookieWithProperties:[NSDictionary dictionaryWithObjectsAndKeys:@"false",@"HttpOnly",nil]];
[[NSHTTPCookieStorage sharedHTTPCookieStorage] setCookie:cookie];
```
 
```
//清除webView的缓存
[[NSURLCache sharedURLCache] removeAllCachedResponses];

//清除请求
[[NSURLCache sharedURLCache] removeCachedResponseForRequest:self.request];
```
 
```
//不加载缓存
[self.webview loadRequest:[NSURLRequest requestWithURL:[NSURL URLWithString:self.webURL] cachePolicy:NSURLRequestReloadIgnoringLocalCacheData timeoutInterval:10.0]];
```

参考文章：[WKWebView与Js实战(OC版)](https://www.cnblogs.com/jiang-xiao-yan/p/5345893.html)

