@cnName 3 Implementing mPaasAppInterface
@priority 3

# 3 Implementing mPaasAppInterface
[TOC]

## 3.1 Overview

The '@protocol mPaasAppInterface' is implemented by developers of the access party and passes the interface class to mPaaS in the mPaasInit method,  for example, the 'mPaasAppInterfaceImp' class below. 
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
mPaaS acquires the data, usually some configurations, provided by the access application with interfaces of this class. 

## 3.2 Description of mPaasAppInterface 

```
//
//  mPaasAppInterface.h
//  mPaasContext
//
//  Created by shenmo on 1/14/15.
//  Copyright (c) 2015 Alipay. All rights reserved.
//

#import <Foundation/Foundation.h>

// When the mPaaS client framework is used, the inner class DFClientDelegate of the framework will handle callbacks of UIApplicationDelegate. 
// DFClientDelegate will notify the access application of events through mPaasAppInterface in various callback methods. 
// Below are some defined event names and their respective triggering methods. With any additional event required, you can rewrite the methods of DFClientDelegate or contact mPaaS developing personnel. 
typedef NS_ENUM (NSInteger, mPaasAppEventType)
{
    mPaasAppEventBeforeDidFinishLaunching, // Called at the entry of @selector(application:didFinishLaunchingWithOptions:)
    mPaasAppEventAfterDidFinishLaunching, // Called at the end of @selector(application:didFinishLaunchingWithOptions:)
    mPaasAppEventBeforeStartLoader, // Called before initiating the loader. The loader will load launcher, primary window, etc. 
    mPaasAppEventDidReceiveRemoteNotification, // Called at the entry of@selector(application:didReceiveRemoteNotification:)
    mPaasAppEventDidReceiveRemoteNotificationFetchCompletion, // @selector(application:didReceiveRemoteNotification:fetchCompletionHandler:). The returned "@(NSUInteger)" indicates the UIBackgroundFetchResult value to be returned.
    mPaasAppEventDidFailToRegisterForRemoteNotifications, // @selector(application:didFailToRegisterForRemoteNotificationsWithError:)
    mPaasAppEventDidReceiveLocalNotification, // Called at the entry of @selector(application:didReceiveLocalNotification:)
    mPaasAppEventQueryOpenURL, // Called by @selector(application:openURL:sourceApplication:annotation:). The application can decide whether to open a URL by returning "@(YES)" or "@(NO)".
    mPaasAppEventWillResignActive, // Called by @selector(applicationWillResignActive:)
    mPaasAppEventDidEnterBackground, // Called by @selector(applicationDidEnterBackground:)
    mPaasAppEventWillEnterForeground, // Called by @selector(applicationWillEnterForeground:)
    mPaasAppEventDidBecomeActive, // Called by @selector(applicationDidBecomeActive:)
    mPaasAppEventWillTerminate, // Called by @selector(applicationWillTerminate:)
    mPaasAppEventHandleWatch, // Called by @selector(application:handleWatchKitExtensionRequest:reply:)
};

/**
 *  This will be implemented by the application accessed to the mPaaS.
 */
@protocol mPaasAppInterface <NSObject>

@optional

/**
 *  The short name of the application. For example, the wallet is named "alipay". Not used for the moment. You don't need to implement it. 
 */
- (NSString*)appBriefName;

/**
 *  The BundleIdentifier of the application.  Not used for the moment. You don't need to implement it.
 */
- (NSString*)appBundleIdentifier;

/**
 *  The BundleIdentifier of the RC version of the application. It must end with "rc". 
 *  The Alipay wallet has an RC version as the stable output during the development phrase. The RC version uses a different BundleId from the released version, so it has a special logic for processing navigation between schemes. 
 *  If the application connected to mPaas also has an RC version available, this method will be utilized for returning the BundleId of the RC version.  Refer to the appSchemePatternName method below. 
 */
- (NSString*)appRCBundleIdentifier;

#pragma mark Setting Service

/**
 *  Whether to use mPaas Setting Service. 
 *  If yes, the configurations should be written in GatewayConfig.plist file. 
 *  For non-released versions, a switch for selecting the environment URL can be added for the application in System - Settings. 
 *  The method will return whether to use Setting Service. If it returns YES, the URL acquired by the Setting Service will be used as a priority. Otherwise, the network-related callback methods under mPaasAppInterface will be used for obtaining the URL. 
 *  
 *  The access party can modify configurations by defining the Settings.bundle. However, after the name of the selected environment is written into NSUserDefaults, its key must be "kMPSelectedEnvironment". 
 *  With the Setting Service enabled, if mPaaS fails to retrieve the kMPSelectedEnvironment value at initialization, it will read the configuration dictionary with the key of "Release" in the GatewayConfig.plist file by default. 
 *  The default structure of GatewayConfig.plist is as follows: 
 *  Root
 *   |- Debug
 *   |- Pre-release
 *   |- Release
 *        |- mPaasPushAppId             The application ID used for accessing APNS through the mPaaS. It is usually the AppKey without the platform. 
 *        |- mPaasLogServerGateway      The URL of the log server (such as "http://10.218.157.65")
 *        |- mPaasLogProductId          The log application ID. It is usually the AppKey with the platform and the workspaceId (such as "APPKEY_IOS-0000017768").
 *        |- mPaasRpcGateway            The RPC gateway URL (such as "http://42.120.224.143/mgw.htm"). In the development phrase, HTTP can be used. When the application goes online, HTTPS should be used. 
 *        |- mPaasRpcETagURL            The RPC ETag URL (such as "https://mobilegw.alipay.com/rpcetag.html")
 */
- (BOOL)appUseSettingService;

#pragma mark Hotpatch

/**
 *  The key name configured in Security Guard for encrypting the Hotpatch scripts. After Hotpatch module gets this key, it will call the Security Guard interface for encryption. 
 *
 *  @return The key name configured in Security Guard
 */
- (NSString*)appHotpatchScriptEncryptionKey;

#pragma mark Log

/**
 *  URL of the remote log server. When the Setting Service is not used, mPaaS will call back this method. 
 */
- (NSString*)appRemoteLogServerURL;

/**
 *  Name of the log service application. Some arguments may be required. It may be different from the product ID in mPaaS.  When the Setting Service is not used, mPaaS will call back this method. 
 */
- (NSString*)appRemoteLogProductId;

/**
 *  The set of log types to be uploaded by default as defined by the application. 
 *  If it is not defined, the application will upload three types of logs, namely the APLogTypeBehavior, APLogTypeCrash and APLogTypeAuto.  If configurations are pulled down from the server, the server configuration values will apply. 
 *
 *  @return @[@(APLogTypeBehavior), @(APLogTypeCrash)]
 */
- (NSArray*)appDefaultUploadLogTypes;

/**
 *  The log service supports the client report-active feature. After the client reports an active status, you can view the report-active logs by the client on the primary site of mPaaS.  The report-active feature is integrated in the framework.  This method is used to manage the report-active frequency. 
 *  When the application is switched from the background to the foreground, if the interval between the switch and the last report-active activity is shorter than a certain value (in seconds), the client will not report again.  If the value of "0" is passed for the argument, the client will report active every time the application is switched from the background to the foreground. 
 *  This will not affect the cold start. The client will report active for each cold start. 
 *
 *  @return Number of seconds
 */
- (NSInteger)appReportActiveMinIntervalSeconds;

#pragma mark Framework callback

/**
 *  When the client framework is used, DFClientDelegate will take over the UIApplicationDelegate events.  The interface is not transparent to the application, but it will notify the application of the events through the callback. 
 *  In usual cases, you only need to implement the callback function without reloading the DFClientDelegate. 
 */
- (id)appDelegateEvent:(mPaasAppEventType)event arguments:(NSDictionary*)arguments;

#pragma mark Login status

/**
 *  It returns the userId of the current user logged in. If the user is not logged in, it will return nil.  Not used for the moment. You don't need to implement it.
 */
- (NSString*)currentUserId;

/**
 *  It returns the current sessionId. If the user is not logged in, it will return nil.  Not used for the moment. You don't need to implement it.
 */
- (NSString*)currentSessionId;

/**
 *  Names of notifications for successful logins and logouts. Persistent connection, Mtop and other services will get the name of account logout event through this callback function and conduct monitoring. 
 */
- (NSString*)loginSuccessNotificationName;
- (NSString*)logoutNotificationName;

#pragma mark Scheme

/**
 *  For a third-party navigation scheme whose RC version is different from its official version, you need to specify a pattern. Only the scheme containing this pattern will be handled. 
 *  If this field is absent in the configuration file, the framework will not process the navigation of the RC version. 
 */
- (NSString*)appSchemePatternName;

/**
 *  It returns the scheme handler class to be added. The returned handler must be a subclass of DTSchemeHandler.  It returns the string array of the class name. 
 */
- (NSArray*)appSchemeHandlerClasses;

#pragma mark ShareKit

/**
 *  It configures arguments of sharing channels, mainly including the key and secret arguments. This protocol must be implemented if the mPaaS introduces the sharing component. 
 *
 *  @return The configuration dictionary of the desired sharing channel. 
 *  The dictionary format is: @{@"laiwang" : @{@"key" : @"your_key", @"secret" : @"your_secret"},
            @"weixin" : {}, @"weibo" : {}, @"qq" : {}};
    A decimal APPID can be passed as the QQ key value. 
 */
- (NSDictionary*)appShareKitConfig;

#pragma mark PushService

/**
 *  The default URL of the Push provider server, used when the Push service is checked.  Not used for the moment. You can ignore it.
 */
- (NSString*)appPushProviderServerURL;

/**
 *  The AppId of the Push service.   The application ID used for accessing APNS through the mPaaS. It is usually the AppKey without the platform.  If the configuration service is not used, mPaaS will call back this method. 
 */
- (NSString*)appPushAppId;

/**
 *  The application receives the remote push notification.  Not used for the moment. You can ignore it.
 */
- (void)appDidReceiveRemoteNotification:(NSDictionary*)info;

#pragma mark Network configurations

/**
 *  The server address of the RPC service. If the configuration service is not used, mPaaS will call back this method.
 */
- (NSString*)appRPCGatewayURL;

/**
 *  The ETag server address of the RPC service. If the configuration service is not used, mPaaS will call back this method.
 */
- (NSString*)appRPCETagURL;

/**
 *  RPC service will adopt the timeouts defined by the application if this method is implemented. 
 *
 *  @return NSTimeInterval, in seconds. 
 */
- (NSTimeInterval)appRPCTimeoutInterval;

/**
 *  The default RPC interceptor container class. If this method is available, RPC will use this class for creating the RPC interceptor at initialization.  Otherwise third-party applications need to set the interceptor on their own. 
 *  An application may have multiple RPC interceptors. You need to add these interceptors to one container interceptor.  All the interceptors, including the container interceptor, must implement the @protocol DTRpcInterceptor interface. 
 */
- (NSString*)appRPCCommonInterceptorClassName;

/**
 *  It defines whether to activate the Sync service on the application. It is only valid when the MPNetworkCtlService exists.  Not used for the moment. You can ignore it.
 */
- (BOOL)appSyncServiceActive;

/**
 *  The AppName, URL and port of the Sync service. It is only valid when the MPNetworkCtlService exists.  Not used for the moment. You can ignore it.
 */
- (NSString*)appSyncServiceAppName;
- (NSString*)appSyncServiceConnectionURL;
- (NSInteger)appSyncServiceConnectionPort;

/**
 *  It provides mPaaS with the domain name for direct access with the IP address. It is only valid when the MPNetworkCtlService exists.  It is currently valid for Spdy and Sync protocols.  Not used for the moment. You can ignore it.
 */
- (NSArray*)appHttpDNSHosts;

/**
 *  Mtop configurations. If this method is not implemented and the Mtop function is enabled, the environment will be by default regarded as an online environment at Mtop initialization. 
 *  Mtop service is only used in the scenario of Alipay account login with password, and when you want to synchronize the login status at the Taobao Mtop gateway. 
 *
 *  @return The Mtop environment. There are three values: 0: online; 1: prepub; 2: daily. 
 */
- (NSInteger)appMtopEnvironment;

#pragma mark Data center

/**
 *  The Data Center feature supports data encryption. The encryption scheme is AES symmetric encryption.  The encryption key is configurable on the application. 
 *
 *  Implement this method, and a 32-byte encryption key will be returned.  The Data Center will adopt the encryption key configured by the application.  The application can manage the key in Security Guard, or write it on the client after encryption confusion. 
 *  If this method is not implemented and the Data Center function is enabled, the Data Center will work out an encryption key with the AppKey passed through the mPaasInit method. If the AppKey remains unchanged, the calculated encryption key remains unchanged, too. 
 *
 *  @return The 32-byte key in NSData. 
 */
- (NSData*)appDataCenterDefaultCryptKey;

@end
```
