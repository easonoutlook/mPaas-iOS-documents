@cnName H5 Container
@priority 6

# H5 Container

[TOC]

## 1 Introduction

H5 Container is composed of the kernel and shell. 

The kernel is the Poseidon.framework which is re-encapsulation of UIWebview. It offers abstract objects of the container, JSBrigde, unified URL intercept, event dispatch, plug-in managing, JSAPI managing and other functions. 

The shell is a simple instance of the kernel. If offers a webViewController and webview for external use. It also provides the universal JSAPI, universal plug-ins, and simple UI interfaces. 

## 2 Features

1. Abundant universal JSAPI (See details at http://ux.alipayinc.com/index.php/H5%E5%AE%B9%E5%99%A8%E6%96%87%E6%A1%A3).   
2. It supports adding self-defined JSAPI. 
3. Customized UI elements. 

## 3 Module access

1. Import mPaas.framework to the project. 
2. Add the iOS system framework the container is dependent on:   CoreTelephony, SystemConfiguration, MobileCoreServices, MessageUI, EventKit, AudioToolbox, CoreMotion, QuartzCore, and CFNetwork.
3. Link H5Service.bundle and Poseidon.bundle to the root directory of the main project. The two bundles will be dispatched to the developer together with mPaas.framework. 

After the module is accessed, you can evoke an H5 container with the codes below: 
```
H5WebViewController *vc = [[H5Service sharedService] createWebViewController:@{@"url":@"http://ux.alipay-inc.com/ftp/h5/toastTest.html"} JSApis:nil withDelegate:self];
[self.navigationController pushViewController:vc animated:YES]; 
```

## 4 Self-defined UserAgent

Configure the customUA field in the startup arguments of the container. 
```
H5WebViewController *vc = [[H5Service sharedService] createWebViewController:@{@"url":@"http://ux.alipay-inc.com/ftp/h5/toastTest.html",@"customUA":@"AliApp(AM/3.0.0.150731) AlipayMerchant/3.0.0.150731"} JSApis:nil withDelegate:self];
[self.navigationController pushViewController:vc animated:YES];
```

## 5 Adding JSAPI

#### 5.1 Modifying Poseidon-Config.plist

1. Create a new class inherited from the  JsApiHandlerBase, and a handler:context:callback: function will be provided for processing argument input and result output. 
2. Add a new record in the JsApis array in Poseidon-Config.plist. The record should contain the class name and the API name exposed to JS, declaring that the JSAPIs here will be registered one by one at the container startup. 

> This practice is not recommended. We suggest the accessed application manage the newly added APIs and conduct dynamic registration when the application is run.  Because after the Poseidon-Config file is modified, you need to pay attention to the overwrite issue when upgrading the H5 container. 

#### 5.2 Runtime dynamic registration

1. Define the JsAPI name in the format of “business name.method name”, such as:   wealth.getMoney. 
2. Acquire the h5service instance. 
3. Register JsAPI through h5service. 

```
static NSString *jsApiName = @"wealth.getMoney";
H5Service *h5Service = [H5Service sharedService];
PSDJsApiHandlerBlock handlerBlock = ^(NSDictionary *data, PSDContext *context, PSDJsApiResponseCallbackBlock responseCallbackBlock) {
    // The data refers to the arguments passed from the page. 
    // dosomething
    // ...
    // The h5webviewcontroller can be obtained in PSDContext.
    // For callback to the page, invoke the responseCallbackBlock. 
};

// Unregister the JsAPI first. 
[h5Service unregisterApiName:jsApiName];
// Then register the JsAPI. 
[h5Service registerApis:@{jsApiName: handlerBlock}];
```
	
## 6 New plug-in

1. Create a new class inherited from the PluginBase or implement PSDPluginProtocol.  Each plug-in will receive event notifications. You can receive event notifications by implementing the handleEvent: method and perform corresponding operations on an event. 
2. The timing of plug-in registration can be summarized to two scenarios.  The first one, like the JSAPI, is to add new records in the Plugins array in Poseidon-Config.plist. This category of plug-ins will register JsAPI at the container startup and listens to all the events. The second one is to register plug-ins dynamically before starting a specific H5 application. 
3. Plug-ins are referenced by weak references. Please hold them yourself. 

```
Plugin4SafePay *payPlugin = [[Plugin4SafePay alloc] init];
[self.plugins addObject:payPlugin];
[self.session addEventListener:kEvent_Navigation_All withListener:payPlugin useCapture:NO];
```
