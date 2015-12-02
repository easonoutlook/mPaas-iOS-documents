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
H5WebViewController *vc = [[H5Service sharedService] createWebViewController:@{@"url":@"http://ux.alipay-inc.com/ftp/h5/toastTest.html"} JSApis:nil withDelegate:self];
[self.navigationController pushViewController:vc animated:YES]; 
```

## 4 自定义 UserAgent

在容器启动参数里面配置 customUA 字段
```
H5WebViewController *vc = [[H5Service sharedService] createWebViewController:@{@"url":@"http://ux.alipay-inc.com/ftp/h5/toastTest.html",@"customUA":@"AliApp(AM/3.0.0.150731) AlipayMerchant/3.0.0.150731"} JSApis:nil withDelegate:self];
[self.navigationController pushViewController:vc animated:YES];
```

## 5 新增 JSApi

#### 5.1 修改 Poseidon-Config.plist

1. 新建一个类继承自 JsApiHandlerBase，会提供一个 handler:context:callback:函数来处理参数输入和结果输出；
2. 在 Poseidon-Config.plist 的 JsApis 数组里新增一条记录，记录里包含类名和暴露给 JS 的 API 名称，声明在这里的 JSApi 会在容器启动的时候一一注册进去。

> 不推荐这种做法，建议接入应用自己管理新增 API，运行时动态注册。因为修改了 Poseidon-Config 文件后，升级 H5 容器时需要注意覆盖问题。

#### 5.2 运行时动态注册

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

1. 新建一个类继承自 PluginBase 或者实现 PSDPluginProtocol。每一个插件都会收到事件通知，可以通过实现 handleEvent:这个代理方法来接收事件通知，对某一个事件进行相应处理；
2. 插件的注册时机有两种。第一种是跟 JSApi 一样，在 Poseidon-Config.plist 的 Plugins 数组里新增记录，此类插件会在容器启动的时候进行注册并监听所有事件；第二种，在启动特定 H5 应用前，动态注册插件；
3. 插件是弱引用，请自己持有。

```
Plugin4SafePay *payPlugin = [[Plugin4SafePay alloc] init];
[self.plugins addObject:payPlugin];
[self.session addEventListener:kEvent_Navigation_All withListener:payPlugin useCapture:NO];
```