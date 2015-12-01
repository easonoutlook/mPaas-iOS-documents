@cnName 4 进阶指南
@priority 4

# 4 进阶指南
[TOC]

## 4.1 处理 Scheme


### 4.1.1 DTSchemeService & DTSchemeHandler

框架公开了 DTSchemeService 服务和 DTSchemeHandler 基类。三方应用接入框架后，只需要在自己工程的 MobileRuntime.plist 里将 DTSchemeService 服务配置进去即可。

![a70d082560d622e11294e893966b609e](https://t.alipayobjects.com/images/rmsweb/T1EWtiXa4XXXXXXXXX.png)

处理不同scheme 的方法，可以实现在不同的 DTSchemeHandler 的子类中。
```C
/**
 * \code DFSchemeHandler 类用于处理操作系统对 [UIApplicationDelegate application:openURL:sourceApplication:annotation] 的
 * 调用机制。
 */
@interface DTSchemeHandler : NSObject

/**
 * 试图注册一个 <code>DFSchemeHandler</code> 的子类，使其可以处理打开链接。
 *
 * @param handlerClass 要注册的 <code>DFSchemeHandler</code> 的子类。
 *
 * @return 注册成功返回 YES， 否则返回 NO。如果出现注册失败的唯一情况就是，指定的类不是 DFURLCommandHandler 的一个子类。
 */
+ (BOOL)registerClass:(Class)handlerClass;

/**
 * 反注册 open URL 的处理类。
 *
 * @param handlerClass open URL 的处理类。
 */
+ (void)unregisterClass:(Class)handlerClass;

+ (BOOL)canHandleOpenURL:(NSURL *)aURL;

- (BOOL)handleOpenURL:(NSURL *)aURL userInfo:(NSDictionary *)userInfo;

@end

```

应用重新实现 mPaasAppInterface 协议中的如下方法：
```C
/**
 *  返回需要添加的 scheme 处理器类名，需要为 DTSchemeHandler 的子类。返回类名的字符串数组。
 */
- (NSArray*)appSchemeHandlerClasses;
```
将 Scheme handler 子类的类名装在数组里返回。（数组元素为 NSString）

### 4.1.2 不使用 SchemeHandler

如果不想使用 SchemeHandler 的处理方式，还可以在 mPaasAppInterface 协议的 appDelegateEvent 方法里处理 mPaasAppEventQueryOpenURL 这个事件。

```C
- (id)appDelegateEvent:(mPaasAppEventType)event arguments:(NSDictionary*)arguments
{
    if (event == mPaasAppEventQueryOpenURL) {
        
    }
    return @(YES);
}
```

## 4.2 处理 APNS


### 4.2.1 获取Device Token

推荐应用创建一个 PushService 来处理 APNS 的 DeviceToken。App 可以注册监听通知 UIApplicationDidRegisterForRemoteNotifications，DFClientDelegate 类在获取到 Device Token 时，会将 token 用该通知返回。代码如下：

```C
@protocol PushService <DTService>

@property (nonatomic, strong, readonly) NSString* pushToken;

@end

@interface PushServiceImpl : NSObject <PushService>
{
    NSString* _pushToken;
}
@end

@implementation PushServiceImpl

@synthesize pushToken = _pushToken;

- (void)start
{
    NSLog(@"PushServiceImpl started");
    
    // 注册推送
#ifdef __IPHONE_8_0    //ios8 注册推送
    if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 8.0f) {
        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge                                                                                         | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil];
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    }
    else
#endif
    {
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes:UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge];
    }
    
    [[NSNotificationCenter defaultCenter] addObserver:self
                                             selector:@selector(applicationDidRegisterForRemoteNotifications:)
                                                 name:UIApplicationDidRegisterForRemoteNotifications object:nil];
}

- (void)applicationDidRegisterForRemoteNotifications:(NSNotification *)notification
{
    NSString* token = [[notification.object description] stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
    _pushToken = [token stringByReplacingOccurrencesOfString:@" " withString:@""];
    NSLog(@"%@", token);
}

@end
```

### 4.2.2 上报 Device Token

应用后台开发同学需要为客户端提供 RPC 接口，负责上报 Device Token。如果需要使用登录名（ userId）推通知，还需要在上报 Device Token 时绑定应用的登录账号。具体请联系应用的后台同学。

### 4.2.3 接收 Push 通知

DFClientDelegate 类在接收到 Remote | Local Notifications时会抛下面两个通知，并将 userInfo 放在 NSNotification 的 userInfo 里返回。

```C
NSString *const UIApplicationDidReceiveRemoteNotification = @"UIApplicationDidReceiveRemoteNotification";
NSString *const UIApplicationDidReceiveLocalNotification = @"UIApplicationDidReceiveLocalNotification";
```

也可以使用 mPaasAppInterface 定义的下面几个 Event 获得，userInfo 会放在字典中返回。
```C
- (id)appDelegateEvent:(mPaasAppEventType)event arguments:(NSDictionary*)arguments;
```

```C
mPaasAppEventDidReceiveRemoteNotification, // @selector(application:didReceiveRemoteNotification:)进入时调用
mPaasAppEventDidReceiveRemoteNotificationFetchCompletion, // @selector(application:didReceiveRemoteNotification:fetchCompletionHandler:)，返回"@(NSUInteger)"表示要返回的 UIBackgroundFetchResult 的值
mPaasAppEventDidReceiveLocalNotification, // @selector(application:didReceiveLocalNotification:)进入时调用
```

## 4.3 Crash 捕获与上报

移动客户端框架集成了 Crash 上报功能，其中 Crash 监控封装了开源 PLCrashReporter。具有以下特点。

* 全自动化捕获 Crash 日志，记录日志，上报日志。使用者无须关心。
* Crash 日志内容更详细，可以获取窗口栈、手势等信息。
* 后台查看反解日志、日志过滤查询。

### 4.3.1 开启 Crash 上报功能

```C
#import <UIKit/UIKit.h>
#import "mPaasAppInterfaceImp.h"

int main(int argc, char * argv[]) {
    enable_crash_reporter_service();
    @autoreleasepool {
        mPaasInit(@"THFUND_IOS", [mPaasAppInterfaceImp class]);
        return UIApplicationMain(argc, argv, @"DFApplication", @"DFClientDelegate");
    }
}
```
在 main 函数中调用 enable_crash_reporter_service()方法开启日志上报功能。（日志上报需要依赖监控系统，配置监控网关地址）

## 4.4 Apple Watch

框架 DFClientDelegate 类会处理 UIApplication 的代理方法：
```
- (void)application:(UIApplication *)application handleWatchKitExtensionRequest:(NSDictionary *)userInfo reply:(void(^)(NSDictionary *replyInfo))reply
```
监听通知，原始参数 userInfo 与 reply 会放在字典里，通过通知的 userInfo 返回。
```
NSString *const UIApplicationWatchKitExtensionRequestNotifications = @"UIApplicationWatchKitExtensionRequestNotifications";
```
或使用 mPaasAppInterface 定义的 appDelegateEvent 方法处理 mPaasAppEventHandleWatch 事件。
```C
- (id)appDelegateEvent:(mPaasAppEventType)event arguments:(NSDictionary*)arguments;
```