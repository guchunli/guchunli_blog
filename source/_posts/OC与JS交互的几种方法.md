---
title: OC与JS交互的几种方法
date: 2017-07-04 11:59:35
categories: 笔记
tags: [OC,JS]
---

OC与JS交互的几种方法
1.JavaScriptCore
2.WebViewJavascriptBridge

<!--more-->
# JavaScriptCore
## 通过JSContext实现OC与JS的交互
1.在webViewDidFinishLoad方法中创建JSContext对象,获取该UIWebview的javascript执行环境
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

2.OC调用JS方法并传递一个参数
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

3.JS调用OC方法并传递一个参数
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
1.通过cocoapods或手动导入WebViewJavascriptBridge框架
2.OC端:创建webview,与bridge对象建立联系
```
// 开启日志
[WebViewJavascriptBridge enableLogging];

// 指定webview，建立JS与OjbC的沟通桥梁
self.bridge = [WebViewJavascriptBridge bridgeForWebView:webView];
[self.bridge setWebViewDelegate:self];
```

3.JS端：创建setupWebViewJavascriptBridge
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

3.JS调用OC方法
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

4.OC调用JS方法
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
