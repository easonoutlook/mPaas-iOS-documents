@cnName Sharing Component
@priority 3

# Sharing Component

[TOC]

## 1 Introduction

The sharing component MPShareKit offers the feature of sharing to micro blogs, WeChat, friends in Alipay wallet, QQ, Laiwang, text messages and other channels. It provides a unified interface to the developers so that they do not need to cope with the differences between various SDK interfaces. 

## 2 Access channel

Before accessing the channels, you must apply for an account on the official websites of various channels. The websites are as follows:   
* Weibo:   http://open.weibo.com/  
* WeChat:   https://open.weixin.qq.com/   
* QQ:   http://open.qq.com/   
* Alipay:   http://open.alipay.com/index.htm 
* Laiwang: Contact mPaaS developer

## 3 Accessing SDK

MPShareKit carries the resource packages for multiple channels which should be added to the application. In addition, the urlScheme (available in various sharing channels) should be added to the Info.plist file of the application. 

* The schemes of WeChat and Laiwang for callback of the source application are both the allocated 'key'. 
* The scheme of Weibo is '"wb"+key'.
* The scheme of QQ is '"tencent"+APPID'. 
* The identifier of Alipay is "alipayShare" and its scheme is the allocated 'appID'. 

For details, see official documents of the channels. 

## 4 Initializing key of sharing channel

The mPaaS MPShareKit offers two ways to register keys. They differ with the client frameworks. 

### 4.1 When client framework Is used

The method under the mPaasAppInterface protocol is implemented and the key and secret values are returned for the method in the form of a dictionary. 
```C
- (NSDictionary*)appShareKitConfig
{
    /**  
     *  The key and secret are used by the demo application for sharing purpose only. Laiwang needs to manually designate the source application for sharing, while other channels do not require this operation.  Contact @粒尘 of Laiwang who will assign the key and secret. 
     */  
    NSDictionary *configDic = @{@"laiwang" : @{@"key" : @"your_key", @"secret" : @"your_secret", @"appname": @"Name of source application of the sharing"},
                            @"weixin" : @{@"key":@"wxc5c09c98c276ac86", @"secret":@"d56057d8a43031bdc178991f6eb8dcd5"},
                            @"weibo" : @{@"key":@"1877934830", @"secret":@"1067b501c42f484262c1803406510af0"},
                            @"qq" : @{@"key":@"1104122330", @"secret":@"WyZkbNmE6d0rDTLf"}, 
                            @"alipay" : @{@"key":@"2015060900117932"/*The bundleID for the key is "com.alipay.share.demo". If it is used for testing, modify it to the key you applied or modify bundleID to "com.alipay.share.demo"*/}
                            };
    return configDic;
}
``` 

### 4.2 When client framework is not used

Register the key in the initialization method of the accessed application. 
```C
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
	// The key and secret are used by the demo application for sharing purpose only. Laiwang needs to manually designate the source application for sharing, while other channels do not require this operation. Contact @粒尘 of Laiwang who will assign the key and secret.
	NSDictionary *dic = @{@"laiwang" : @{@"key" : @"your_key", @"secret" : @"your_secret", @"appname": @"Share by application name"},
	@"weixin" : @{@"key":@"wxc5c09c98c276ac86", @"secret":@"d56057d8a43031bdc178991f6eb8dcd5"},
	@"weibo" : @{@"key":@"1877934830", @"secret":@"1067b501c42f484262c1803406510af0"},
	@"qq" : @{@"key":@"1104122330", @"secret":@"WyZkbNmE6d0rDTLf"},
	@"alipay" : @{@"key":@"2015060900117932"}/*The bundleID for the key is "com.alipay.share.demo". If it is used for testing, modify it to the key you applied or modify bundleID to "com.alipay.share.demo"*/}

	[APSKClient registerAPPConfig:dic];
}
```  

## 5 Interface description

### 5.1 Evoke Sharing Launchpad

You can specify the channels to be displayed while evoking the Sharing Launchpad. 
```C
NSArray *channelArr = @[kAPSKChannelQQ, kAPSKChannelLaiwangContacts, kAPSKChannelLaiwangTimeline, kAPSKChannelWeibo, kAPSKChannelWeixin, kAPSKChannelCopyLink];

self.launchPad = [[APSKLaunchpad alloc] initWithChannels:channelArr sort:NO];
self.launchPad.delegate = self;  
[self.launchPad showForView:[[UIApplication sharedApplication] keyWindow] animated:YES];
```

### 5.2 Completing sharing operation
Handle sharing in the callback of 'sharingLaunchpad' function in '@protocol APSKLaunchpadDelegate'. 
```C
- (void)sharingLaunchpad:(APSKLaunchpad *)launchpad didSelectChannel:(NSString *)channelName
{
    [self shareUrl:channelName];
    [self.launchPad dismissAnimated:YES];
}

- (void)shareUrl:(NSString*)channelName
{
	//Generate data and call corresponding channels for sharing. 
    APSKMessage *message = [[APSKMessage alloc] init];
    message.contentType = @"url";//The types are "text", "image", and "url". 
    message.content = [NSURL URLWithString:@"www.sina.com.cn"];
    message.icon = [UIImage imageNamed:@"1"];
    message.title = @"title";
    message.description = @"description";

    APSKClient *client = [[APSKClient alloc] init];

    [client shareMessage:message toChannel:channelName completionBlock:^(NSError *error, NSDictionary *userInfo) {
        //userInfo is the extended information. 
        if(!error)
        {
            //your logistic
        }
        NSLog(@"error = %@", error);
    }];    
}
```
### 5.3 Returning from channel application

When the client framework is used, no processing is required and the framework will take charge.  When the client framework is not used, refer to the codes below: 

```C
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
{
    //After the sharing is complete, return from the channel application to the source application. 
    BOOL ret;  
    ret = [APSKClient handleOpenURL:url];  
    return ret;
}
```
