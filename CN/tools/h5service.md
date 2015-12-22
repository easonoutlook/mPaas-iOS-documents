@cnName H5 容器
@priority 6

# H5 容器

[TOC]

## 1 简介

H5 容器由内核与壳两部分组成。

内核为 Poseidon.framework，是对 UIWebview 的再封装，提供了容器抽象对象、JSBrigde、URL 统一拦截、事件分发、插件管理、JSApi 管理等功能；

壳是内核的一个简单实例，提供一个 webViewController 和 webview 供外部使用，同时提供了通用的 JSApi、通用的插件、简单的 UI 界面。

## 2 功能特点

1. 丰富的通用 JSApi（详细文档见http://ux.alipayinc.com/index.php/H5%E5%AE%B9%E5%99%A8%E6%96%87%E6%A1%A3） 
2. 支持添加自定义 JSApi
3. 可定制化的 UI 元素


## 3 模块接入

1. 将 mPaas.framework 添加到工程中。
2. 添加容器依赖的 iOS 系统框架： CoreTelephony、SystemConfiguration、MobileCoreServices、MessageUI、EventKit、AudioToolbox、CoreMotion、QuartzCore、CFNetwork。
3. 将H5Service.bundle 和 Poseidon.bundle 链入到主工程根目录下，这两个 Bundle 会随着 mPaas.framework 一起分发给开发者。

接入模块后，可以使用下面代码唤起一个 H5 容器
```
H5WebViewController *vc = [[H5Service sharedService] createWebViewController:@{@"url":@"http://ux.alipay-inc.com/ftp/h5/toastTest.html"} JSApis:nil withDelegate:nil];
[self.navigationController pushViewController:vc animated:YES]; 
```

## 4 自定义 UserAgent

在容器启动参数里面配置 customUA 字段
```
H5WebViewController *vc = [[H5Service sharedService] createWebViewController:@{@"url":@"http://ux.alipay-inc.com/ftp/h5/toastTest.html",@"customUA":@"AliApp(AM/3.0.0.150731) AlipayMerchant/3.0.0.150731"} JSApis:nil withDelegate:nil];
[self.navigationController pushViewController:vc animated:YES];
```

## 5 新增 JSApi

1. 定义 JsApi 名称，业务名.方法名，譬如： wealth.getMoney。
2. 获取h5service 实例；
3. 通过 h5service 注册 JsApi；

```
static NSString *jsApiName = @"wealth.getMoney";
H5Service *h5Service = [H5Service sharedService];
PSDJsApiHandlerBlock handlerBlock = ^(NSDictionary *data, PSDContext *context, PSDJsApiResponseCallbackBlock responseCallbackBlock) {
    // data 是页面传递过来的参数
    // dosomething
    // ...
    // PSDContext 中可以获取h5webviewcontroller
    // 如果需要回调给页面，调用 responseCallbackBlock
};

// 先移除
[h5Service unregisterApiName:jsApiName];
// 再注册
[h5Service registerApis:@{jsApiName: handlerBlock}];
```
	
## 6 新增插件

1. 新建一个类继承自 PluginBase。每一个插件都会收到事件通知，可以通过实现 handleEvent:这个代理方法来接收事件通知，对某一个事件进行相应处理；
2. 打开容器前，在session里把新建的插件注册进去，并指定监听哪种事件。

```
H5Service *h5Service = [H5Service sharedService];
H5WebViewController *vc = [h5Service createWebViewController:@{@"url":@"http://ux.alipay-inc.com/ftp/h5/toastTest.html"} JSApis:nil withDelegate:nil];

// 开始注册插件
Plugin4RemoteLog *remoteLogPlugin = [[Plugin4RemoteLog alloc] init];
[h5Service.lastSession.plugins addObject:remoteLogPlugin];
[h5Service.lastSession.session addEventListener:kEvent_Page_All
                      withListener:remoteLogPlugin
                        useCapture:NO];
// 注册插件结束。Plugin4RemoteLog这个插件监听了kEvent_Page_All事件。具体还有哪些事件可监听可看PSDDefine.h。

[self.navigationController pushViewController:vc animated:YES];
```

```
// Plugin4RemoteLog.h的示例代码
#import "PluginBase.h"
@interface Plugin4RemoteLog : PluginBase
@end
```
```
// Plugin4RemoteLog.m的示例代码
#import "Plugin4RemoteLog.h"
@implementation Plugin4RemoteLog
- (void)handleEvent:(PSDEvent *)event
{
    [super handleEvent:event];
    __weak H5PSDController *weakVC = event.context.currentViewController;
    if ([kEvent_Page_Load_Start isEqualToString:event.eventType]) {
        [weakVC setExpando:kExpandPropertyKey_RemoteLogCallCount withValue:[NSNumber numberWithInt:0]];
    }
}
@end
```

```
- (void)handleEvent:(PSDEvent *)event
{
	[super handleEvent:event];
    
	// 在Response回来时，看Response的header里是否带有某个key
    if ([event.eventType isEqualToString:kEvent_Proxy_Request_Response_Handler] && [event isKindOfClass:[PSDProxyEvent class]]) {
        PSDProxyEvent *proxyEvent = (PSDProxyEvent *)event;
        NSHTTPURLResponse *response = nil;
        if ([proxyEvent.response isKindOfClass:[NSHTTPURLResponse class]]) {
        	response = (NSHTTPURLResponse *)proxyEvent.response;
            NSDictionary *headers = [response allHeaderFields];
            if ([headers objectForKey:kResponseHeaderFieldSpecialCode]) {
            	specialCode = [[headers objectForKey:kResponseHeaderFieldSpecialCode] intValue];
            }
        }
    }
    
    // 在页面主文档开始加载时，判断是淘宝域名就种一些cookie
    if ([kEvent_Navigation_Start isEqualToString:event.eventType] && [event isKindOfClass:[PSDNavigationEvent class]]) {
        PSDNavigationEvent *naviEvent = (PSDNavigationEvent *)event;
        NSURL *currentURL = naviEvent.request.URL;
        // 判断是淘宝域名就种一些cookie
    }
    
    // 监听某个资源开始加载
    if ([kEvent_Proxy_Request_Start_Handler isEqualToString:event.eventType] && [event isKindOfClass:[PSDProxyEvent class]]) {
    	PSDProxyEvent *proxyEvent = (PSDProxyEvent *)event;
    	NSString *regex = @".*\\.(alipay\\.com|alipay\\.net)";
    	NSPredicate *predicate = [NSPredicate predicateWithFormat:@"host MATCHES[cd] %@", regex];
        if ([predicate evaluateWithObject:proxyEvent.request.URL]) {
        	[self reportReqStart];
        }
    }
}
```