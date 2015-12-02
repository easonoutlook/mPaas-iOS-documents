@cnName 3 实现 mPaasAppInterface
@priority 3

# 3 实现 mPaasAppInterface
[TOC]

## 3.1 概述

`@protocol mPaasAppInterface`这个接口是由接入方开发者实现的接口，并在 mPaasInit 方法里传入接口类。如下的`mPaasAppInterfaceImp`类。
```
#import <UIKit/UIKit.h>
#import "mPaasAppInterfaceImp.h"

int main(int argc, char * argv[]) {
    @autoreleasepool {
        mPaasInit(@"productId", [mPaasAppInterfaceImp class]);
        return UIApplicationMain(argc, argv, @"DFApplication", @"DFClientDelegate");
    }
}
```
移动框架会使用这个类的接口来获取接入应用提供的数据，通常为一些配置。

## 3.2 mPaasAppInterface 接口说明

```
#import <UIKit/UIKit.h>

// 当使用客户端框架时，框架内部类DFClientDelegate将负责处理UIApplicationDelegate回调。
// DFClientDelegate会在各回调方法中，通过mPaasAppInterface将事件通知给接入应用。
// 下面为已经定义的事件名与各自触发方法，如果有需要添加事件，可以重写DFClientDelegate相关方法或联系开发同学。
typedef NS_ENUM (NSInteger, mPaasAppEventType)
{
    mPaasAppEventBeforeDidFinishLaunching, // @selector(application:didFinishLaunchingWithOptions:)进入时调用
    mPaasAppEventAfterDidFinishLaunching, // @selector(application:didFinishLaunchingWithOptions:)结束时调用
    mPaasAppEventBeforeStartLoader, // 启动加载器前调用，加载器加载launcher、主window等
    mPaasAppEventDidReceiveRemoteNotification, // @selector(application:didReceiveRemoteNotification:)进入时调用
    mPaasAppEventDidReceiveRemoteNotificationFetchCompletion, // @selector(application:didReceiveRemoteNotification:fetchCompletionHandler:)，返回"@(NSUInteger)"表示要返回的UIBackgroundFetchResult的值
    mPaasAppEventDidFailToRegisterForRemoteNotifications, // @selector(application:didFailToRegisterForRemoteNotificationsWithError:)
    mPaasAppEventDidReceiveLocalNotification, // @selector(application:didReceiveLocalNotification:)进入时调用
    mPaasAppEventQueryOpenURL, // @selector(application:openURL:sourceApplication:annotation:)调用，app决定是否可以打开某个URL，返回"@(YES)"或"@(NO)"
    mPaasAppEventWillResignActive, // @selector(applicationWillResignActive:)调用
    mPaasAppEventDidEnterBackground, // @selector(applicationDidEnterBackground:)调用
    mPaasAppEventWillEnterForeground, // @selector(applicationWillEnterForeground:)调用
    mPaasAppEventDidBecomeActive, // @selector(applicationDidBecomeActive:)调用
    mPaasAppEventWillTerminate, // @selector(applicationWillTerminate:)调用
    mPaasAppEventHandleWatch, // @selector(application:handleWatchKitExtensionRequest:reply:)调用
};

/**
 *  这个需要接入 app 来实现
 */
@protocol mPaasAppInterface <NSObject>

@optional

/**
 *  应用的简短名称，比如钱包叫“alipay”。暂时无用，可以不实现。
 */
- (NSString*)appBriefName;

/**
 *  应用的BundleIdentifier。暂时无用，可以不实现。
 */
- (NSString*)appBundleIdentifier;

/**
 *  应用RC版的BundleIdentifier，必须以rc结尾。
 *  支付宝钱包有RC版本作为开发阶段的稳定输出版，RC与线上版使用不同的BundleId，所以在处理scheme跳转时有特殊逻辑。
 *  如果接入的应用也有RC版，需要用这个方法返回RC版的BundleId。参考下面的appSchemePatternName方法。
 */
- (NSString*)appRCBundleIdentifier;

#pragma mark 设置服务

/**
 *  是否要使用设置服务。
 *  如果使用设置服务，需要将配置写在GatewayConfig.plist文件中。
 *  在非发布版本，还可以在系统-设置里为应用添加一个选择环境地址的开关。
 *  返回是否要使用设置服务，如果是YES，会优先使用设置服务获取的地址，否则会使用mPaasAppInterface下面网络相关的回调来获取地址。
 *  
 *  接入方可以自己定义Settings.bundle来修改配置，不过选择的环境名称写入到NSUserDefaults后key必须为“kMPSelectedEnvironment”。
 *  如果使用了设置服务，初始化时读取不到kMPSelectedEnvironment的值，会默认去GatewayConfig.plist文件中读取key为“Release”的配置字典。
 *  默认的GatewayConfig.plist结构为：
 *  Root
 *   |- Debug
 *   |- Pre-release
 *   |- Release
 *        |- mPaasPushAppId             接入APNS使用的应用Id，通常为不带平台的APPKEY
 *        |- mPaasLogServerGateway      日志服务器地址（类似“http://10.218.157.65”）
 *        |- mPaasLogProductId          日志应用Id，通常为带平台的APPKEY加workspaceId（类似“APPKEY_IOS-0000017768”）
 *        |- mPaasRpcGateway            RPC网关地址（类似“http://42.120.224.143/mgw.htm”），开发阶段可以使用http，线上应该使用https。
 *        |- mPaasRpcETagURL            RPC ETag地址（类似“https://mobilegw.alipay.com/rpcetag.html”）
 */
- (BOOL)appUseSettingService;

#pragma mark Hotpatch

/**
 *  配置在无线保镖中的，用于Hotpatch脚本加密的key的名字。Hotpatch模块拿到这个key，会调用无线保镖的接口进行加密。
 *
 *  @return 配置在无线保镖中的key的名字
 */
- (NSString*)appHotpatchScriptEncryptionKey;

#pragma mark Log

/**
 *  远程日志服务器的地址，当不使用设置服务时，会回调该方法。
 */
- (NSString*)appRemoteLogServerURL;

/**
 *  日志服务的应用名，可能需要加一些参数，与product id可能不同。当不使用设置服务时，会回调该方法。
 */
- (NSString*)appRemoteLogProductId;

/**
 *  应用定义的默认进行上传的日志种类的集合。
 *  如果不定义，当前默认上传APLogTypeBehavior/APLogTypeCrash/APLogTypeAuto三种log。如果从服务器拉下来了配置，会使用配置值。
 *
 *  @return @[@(APLogTypeBehavior), @(APLogTypeCrash)]
 */
- (NSArray*)appDefaultUploadLogTypes;

/**
 *  日志服务支持客户端报活功能，报活后可以在网站上看到客户端报活记录。报活功能已经集成在框架中。该方法用于控制报活的频率。
 *  从后台切回前台时，距离上次报活时间少于多少秒时，不再报活。如果传0，每次后台切回前台都会报活。
 *  这个不影响冷启动，如果冷启动，每次都会报活。
 *
 *  @return 返回秒数
 */
- (NSInteger)appReportActiveMinIntervalSeconds;

#pragma mark 框架的回调

/**
 *  使用客户端框架时，DFClientDelegate接管UIApplicationDelegate事件。接口对应用不透明，但是会通过该回调将事件通知给应用。
 *  通常只需要实现回调函数即可，不需要重载DFClientDelegate。
 */
- (id)appDelegateEvent:(mPaasAppEventType)event arguments:(NSDictionary*)arguments;

/**
 *  每个继承自DTViewController的子VC，都会被默认设置为一个背景色，如果实现这个方法，框架会回调并取到应用指定的颜色。
 */
- (UIColor*)appBaseViewControllerBackgroundColor;

#pragma mark 登录态

/**
 *  返回当前登录用户userId，如果不是登录态，需要返回nil。目前无用，可以不实现。
 */
- (NSString*)currentUserId;

/**
 *  当前的sessionId，如果不是登录态，需要返回nil。目前无用，可以不实现。
 */
- (NSString*)currentSessionId;

/**
 *  登录成功与退出登录的通知名，长链接、Mtop等服务会通过这个回调获得账户登录退出的事件名并监听。
 */
- (NSString*)loginSuccessNotificationName;
- (NSString*)logoutNotificationName;

#pragma mark Scheme

/**
 *  如果想处理RC与正式版本不同的第三方跳转scheme，需要指定一个patten，当scheme含有此pattern时才处理。
 *  配置文件里如果没有这个字段，框架则不会处理RC版本的跳转。
 */
- (NSString*)appSchemePatternName;

/**
 *  返回需要添加的scheme处理器类名，需要为DTSchemeHandler的子类。返回类名的字符串数组。
 */
- (NSArray*)appSchemeHandlerClasses;

#pragma mark ShareKit

/**
 *  分享渠道的配置参数，主要是key、secret参数，引入了分享组件时一定要实现此协议；
 *
 *  @return 所要分享渠道的配置词典
 *  词典格式为：@{@"laiwang" : @{@"key" : @"your_key", @"secret" : @"your_secret"},
            @"weixin" : {}, @"weibo" : {}, @"qq" : {}};
    qq的key值传入十进制APPID即可；
 */
- (NSDictionary*)appShareKitConfig;

#pragma mark PushService

/**
 *  Push服务器默认地址，当勾选了Push服务时使用。暂时无用，可以忽略。
 */
- (NSString*)appPushProviderServerURL;

/**
 *  Push的AppId。为接入APNS时使用的应用Id，通常为不带平台的APPKEY。不使用配置服务，会回调此方法。
 */
- (NSString*)appPushAppId;

/**
 *  应用接收到远程push。暂时无用，可以忽略。
 */
- (void)appDidReceiveRemoteNotification:(NSDictionary*)info;

#pragma mark 网络配置

/**
 *  RPC的服务器地址，不使用配置服务，会回调此方法。
 */
- (NSString*)appRPCGatewayURL;

/**
 *  RPC的ETag服务器地址，不使用配置服务，会回调此方法。
 */
- (NSString*)appRPCETagURL;

/**
 *  实现此方法，RPC会使用应用定义的超时时间
 *
 *  @return NSTimeInterval，单位秒
 */
- (NSTimeInterval)appRPCTimeoutInterval;

/**
 *  RPC请求需要加签时，使用这个方法返回签名使用的密钥。
 *  一般不需要实现这个方法，RPC模块会默认使用 mPaasInit 方法初始化传入的 APPKEY来签名。
 *
 *  @return 使用在无线保镖里保存的哪个密钥来签名请求。
 */
- (NSString*)appRPCSignKey;

/**
 *  默认的RPC拦截器容器类名，如果有这个方法，RPC初始化时，会使用这个类创建RPC拦截器。否则需要第三方应用自己设置拦截器。
 *  应用可能有多个RPC拦截器，需要将这些拦截器添加到一个容器拦截器中。所有拦截器，包括容器拦截器都需要实现接口 @protocol DTRpcInterceptor
 */
- (NSString*)appRPCCommonInterceptorClassName;

/**
 *  应用是否要使用Sync服务，当MPNetworkCtlService存在时才有效。暂时无用，可以忽略。
 */
- (BOOL)appSyncServiceActive;

/**
 *  Sync服务的应用AppName，链接地址与端口，当MPNetworkCtlService存在时才有效。暂时无用，可以忽略。
 */
- (NSString*)appSyncServiceAppName;
- (NSString*)appSyncServiceConnectionURL;
- (NSInteger)appSyncServiceConnectionPort;

/**
 *  需要转为ip直连的域名，当MPNetworkCtlService存在时有效。目前对Spdy和Sync有效。暂时无用，可以忽略。
 */
- (NSArray*)appHttpDNSHosts;

/**
 *  Mtop配置，如果不实现这个方法并使用了Mtop功能，那么Mtop初始化时会默认为线上环境。
 *  Mtop服务仅用在接入支付宝账密登录，并希望同步淘宝Mtop网关登录态的场景。
 *
 *  @return 返回Mtop环境，0：线上，1：预发，2：日常。
 */
- (NSInteger)appMtopEnvironment;

#pragma mark 统一存储

/**
 *  统一存储功能支持数据加密，加密方法为对称AES加密。加密密钥为应用可配项。
 *
 *  实现这个方法，并返回32字节的加密key。统一存储会使用应用配置的加密key。这个key应用可以使用无线保镖管理，也可以加密混淆后写在客户端。
 *  如果不实现这个方法，并且使用了统一存储功能。统一存储会使用mPaasInit方法传入的appKey计算出一个加密key，appKey不变时算出的加密key也不变。
 *
 *  @return 32字节的key，放在NSData里返回。
 */
- (NSData*)appDataCenterDefaultCryptKey;

@end
```