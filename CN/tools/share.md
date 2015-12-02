@cnName 分享组件
@priority 3

# 分享组件

[TOC]

## 1 简介

分享组件 MPShareKit 提供微博、微信、支付宝钱包朋友、QQ、来往、短信等渠道的分享功能，提供给开发者统一的接口，无须处理各 SDK 的接口差异性。

## 2 接入渠道

接入各渠道之前须在各分享渠道官方网站申请账号，地址如下：  
* 微博： http://open.weibo.com/  
* 微信： https://open.weixin.qq.com/   
* QQ： http://open.qq.com/   
* 支付宝： http://open.alipay.com/index.htm 

## 3 接入 SDK

分享组件会携带多个渠道的资源包，也需要加到应用里，同时在应用的 Info.plist 文件中加入 urlScheme（从各分享渠道获取）

* 微信、来往回调源 APP 的 scheme 均为分配的`key`
* 微博的 scheme 为`"wb"+key`
* QQ 的 scheme 为`"tencent"+APPID`
* 支付宝的 Identifier 为 alipayShare，scheme 为分配的`appID`

详情请参考各渠道的官方文档。

## 4 初始化分享渠道的密钥

移动分享组件提供两种注册密钥的方式，根据是否使用客户端框架而有所不同。

### 4.1 使用客户端框架时

实现协议 mPaasAppInterface 的下列方法，并在此方法中以词典的形式返回 key、secret 的值。
```C
- (NSDictionary*)appShareKitConfig
{
    /**  
     *  此 key 和 secret 为 demo 所使用，仅能够进行分享，来往需要手动指定分享来自应用的名称，其他渠道不需要。来往联系 @粒尘 分配 key 和 secret。
     */  
    NSDictionary *configDic = @{@"laiwang" : @{@"key" : @"your_key", @"secret" : @"your_secret", @"appname": @"分享来源应用的显示名称"},
                            @"weixin" : @{@"key":@"wxc5c09c98c276ac86", @"secret":@"d56057d8a43031bdc178991f6eb8dcd5"},
                            @"weibo" : @{@"key":@"1877934830", @"secret":@"1067b501c42f484262c1803406510af0"},
                            @"qq" : @{@"key":@"1104122330", @"secret":@"WyZkbNmE6d0rDTLf"}, 
                            @"alipay" : @{@"key":@"2015060900117932"/*该 key 对应的 bundleID 为"com.alipay.share.demo", 如需用来测试,请修改为自己申请的 key 或修改 bundleID 为"com.alipay.share.demo"*/}
                            };
    return configDic;
}
``` 

### 4.2 不使用客户端框架时

在接入应用的启动方法中注册密钥。
```C
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    // 此 key 和 secret 为 demo 所使用，仅能够进行分享，来往需要手动指定分享来自应用的名称，其他渠道不需要。来往联系 @粒尘 分配 key 和 secret。
    NSDictionary *dic = @{@"laiwang" : @{@"key" : @"your_key", @"secret" : @"your_secret", @"appname": @"分享来自应用显示名称"},
    @"weixin" : @{@"key":@"wxc5c09c98c276ac86", @"secret":@"d56057d8a43031bdc178991f6eb8dcd5"},
    @"weibo" : @{@"key":@"1877934830", @"secret":@"1067b501c42f484262c1803406510af0"},
    @"qq" : @{@"key":@"1104122330", @"secret":@"WyZkbNmE6d0rDTLf"},
    @"alipay" : @{@"key":@"2015060900117932"}/*该 key 对应的 bundleID 为"com.alipay.share.demo", 如需用来测试,请修改为自己申请的 key 或修改 bundleID 为"com.alipay.share.demo"*/ };

    [APSKClient registerAPPConfig:dic];
}
```  

## 5 接口说明

### 5.1 唤起分享选择面板

在唤起分享面板时可以指定需要显示的渠道
```C
NSArray *channelArr = @[kAPSKChannelQQ, kAPSKChannelLaiwangContacts, kAPSKChannelLaiwangTimeline, kAPSKChannelWeibo, kAPSKChannelWeixin, kAPSKChannelCopyLink];

self.launchPad = [[APSKLaunchpad alloc] initWithChannels:channelArr sort:NO];
self.launchPad.delegate = self;  
[self.launchPad showForView:[[UIApplication sharedApplication] keyWindow] animated:YES];
```

### 5.2 完成分享操作
在`@protocol APSKLaunchpadDelegate`的`sharingLaunchpad `回调中处理分享
```C
- (void)sharingLaunchpad:(APSKLaunchpad *)launchpad didSelectChannel:(NSString *)channelName
{
    [self shareUrl:channelName];
    [self.launchPad dismissAnimated:YES];
}

- (void)shareUrl:(NSString*)channelName
{
    //生成数据，调用对应渠道分享
    APSKMessage *message = [[APSKMessage alloc] init];
    message.contentType = @"url";//类型分"text","image", "url"三种
    message.content = [NSURL URLWithString:@"www.sina.com.cn"];
    message.icon = [UIImage imageNamed:@"1"];
    message.title = @"标题";
    message.description = @"描述";

    APSKClient *client = [[APSKClient alloc] init];

    [client shareMessage:message toChannel:channelName completionBlock:^(NSError *error, NSDictionary *userInfo) {
        //userInfo 为扩展信息
        if(!error)
        {
            //your logistic
        }
        NSLog(@"error = %@", error);
    }];    
}
```
### 5.3 从渠道应用跳回的处理

当使用客户端框架时不需要处理，会由框架负责。当不使用客户端框架时参照下列代码进行处理：

```C
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
{
    //加入分享成功后，从渠道 APP 回到源 APP 的处理
    BOOL ret;  
    ret = [APSKClient handleOpenURL:url];  
    return ret;
}
```