@cnName 4 Guide to Advanced Features
@priority 4

# 4 Guide to Advanced Features
[TOC]

## 4.1 Handling scheme


### 4.1.1 DTSchemeService & DTSchemeHandler

The framework opens up the DTSchemeService and the DTSchemeHandler base class.  Once a third-party application is accessed to the framework, the developer only needs to configure the DTSchemeService into the MobileRuntime.plist of the project. 

![a70d082560d622e11294e893966b609e](https://t.alipayobjects.com/images/rmsweb/T1EWtiXa4XXXXXXXXX.png)

The ways for handling different schemes can be implemented in different subclasses of the DTSchemeHandler. 
```C
/**
 * \code DFSchemeHandler class is used for handling the calling mechanism of the operating system on  [UIApplicationDelegate application:openURL:sourceApplication:annotation]
 * .
 */
@interface DTSchemeHandler : NSObject

/**
 * Try to register a subclass of <code>DFSchemeHandler</code>, and enable it to open links. 
 *
 * @param handlerClass The subclass of the <code>DFSchemeHandler</code> to be registered. 
 *
 * @return YES is returned for successful registration; otherwise, NO. The only case of failed registration would be because the specified class is not a subclass of DFURLCommandHandler. 
 */
+ (BOOL)registerClass:(Class)handlerClass;

/**
 * Unregister the handler class of open URL. 
 *
 * @param handlerClass The handler class of open URL.
 */
+ (void)unregisterClass:(Class)handlerClass;

+ (BOOL)canHandleOpenURL:(NSURL *)aURL;

- (BOOL)handleOpenURL:(NSURL *)aURL userInfo:(NSDictionary *)userInfo;

@end

```

The application re-implements the methods below in the  mPaasAppInterface protocol: 
```C
/**
 *  It returns the scheme handler class to be added. The returned handler must be a subclass of DTSchemeHandler. It returns the string array of the class name.
 */
- (NSArray*)appSchemeHandlerClasses;
```
Return the subclass name of the scheme handler in arrays.  (The array element is NSString.)

### 4.1.2 Not using SchemeHandler

If you don't want to use SchemeHandler, you can use the appDelegateEvent method of mPaasAppInterface protocol for handling mPaasAppEventQueryOpenURL events. 

```C
- (id)appDelegateEvent:(mPaasAppEventType)event arguments:(NSDictionary*)arguments
{
    if (event == mPaasAppEventQueryOpenURL) {
        
    }
    return @(YES);
}
```
@cnName 4.2 Handling APNS
@priority 2

## 4.2 Handling APNS


### 4.2.1 Getting device token

We recommend that the application create a PushService for handling the APNS device token.  The application can register for the monitoring notification service of UIApplicationDidRegisterForRemoteNotifications so that when the DFClientDelegate class gets the device token, it will return the token using the notification.  The codes are as follows: 

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
    
    // Push notification registration
#ifdef __IPHONE_8_0    //Push notification registration of iOS8
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

### 4.2.2 Reporting device token

The developer of the application background should provide the RPC interface for the client for reporting the device token.  If the login name (userId) is employed for pushing the notification, the login account of the application should also be bound to the device token for reporting.  For details, contact the personnel of the application background. 

### 4.2.3 Receiving Push notification

The DFClientDelegate class will throw two notifications as below when it receives Remote | Local Notifications, and returns the userInfo in the userInfo of the NSNotification. 

```C
NSString *const UIApplicationDidReceiveRemoteNotification = @"UIApplicationDidReceiveRemoteNotification";
NSString *const UIApplicationDidReceiveLocalNotification = @"UIApplicationDidReceiveLocalNotification";
```

The userInfo can also be obtained using the several events as follows defined by mPaasAppInterface, in which case the userInfo will be returned in the dictionary. 
```C
- (id)appDelegateEvent:(mPaasAppEventType)event arguments:(NSDictionary*)arguments;
```

```C
mPaasAppEventDidReceiveRemoteNotification, // Called at the entry of@selector(application:didReceiveRemoteNotification:)
mPaasAppEventDidReceiveRemoteNotificationFetchCompletion, // @selector(application:didReceiveRemoteNotification:fetchCompletionHandler:). The returned "@(NSUInteger)" indicates the UIBackgroundFetchResult value to be returned.
mPaasAppEventDidReceiveLocalNotification, // Called at the entry of @selector(application:didReceiveLocalNotification:)
```

## 4.3 Crash capture and reporting

The mPaaS client framework integrates the crash reporting feature, in which the crash monitoring encapsulates open-source PLCrashReporter.  It has the following features: 

* Fully automatic capture of crash logs, logging and log reporting.  Users do not need to care about it. 
* The crash log contents grow more detailed, with the window stack and gesture information available. 
* The background supports the view of the reverse resolution logs, and log filtering and queries. 

### 4.3.1 Enabling crash reporting feature

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
Call enable_crash_reporter_service() method in the main function to enable log reporting.  (The log reporting is dependent on the monitoring system, so the monitoring gateway URL should be configured.)

## 4.4 Apple Watch

The DFClientDelegate class of the framework will handle delegate methods of UIApplication: 
```
- (void)application:(UIApplication *)application handleWatchKitExtensionRequest:(NSDictionary *)userInfo reply:(void(^)(NSDictionary *replyInfo))reply
```
Monitoring notification, the original arguments userInfo and reply will be placed in the dictionary and returned in userInfo of notifications. 
```
NSString *const UIApplicationWatchKitExtensionRequestNotifications = @"UIApplicationWatchKitExtensionRequestNotifications";
```
Or the appDelegateEvent method defined by mPaasAppInterface can be used for handling the mPaasAppEventHandleWatch event. 
```C
- (id)appDelegateEvent:(mPaasAppEventType)event arguments:(NSDictionary*)arguments;
```
