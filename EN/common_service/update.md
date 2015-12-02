@cnName 6 Checking for Updates
@priority 6

# 6 Checking for Updates

## 6.1 Introduction

The mPaaS website offers the online packaging feature. Once the packaging is complete, the developer can choose the package of a specific version for release.  The online application will detect the new version through the update interface and prompts users to download the update.  The developer can also set update prompts, whether to enforce updates and other options for the released package. 
![g1](https://os.alipayobjects.com/rmsportal/IwyWcUCKwOEzIjE.png)

'Note:   App Store regulations prohibit built-in checking for updates in deployed applications.  The checking for updates feature can be used during the development phrase for internal testing.  '

## 6.2 How to use

The feature provides the RPC interface file for checking for updates which can be integrated into the application codes. The application can call the interface on its own.   Sample: 
```
- (void)checkUpdate:(void (^)(MPAAS_CheckUpgradeResp* resp))callback
{
    __block MPAAS_CheckUpgradeResp *result = nil;
    [DTRpcAsyncCaller callAsyncBlock:^{
        MPAAS_CheckUpdateServiceFacade* facade = [[MPAAS_CheckUpdateServiceFacade alloc] init];
        MPAAS_CheckUpgradeReq* request = [[MPAAS_CheckUpgradeReq alloc] init];
        request.appkey = [mPaas() appKey];
        request.version = [[NSBundle mainBundle] objectForInfoDictionaryKey:@"CFBundleVersion"];
        request.systemPlatform = @"ios";
        @try
        {
            result = [facade checkUpdate:request];
        }
        @catch(DTRpcException *exception)
        {
            NSLog(@"%@", exception);
        }
    } completion:^{
        if (result)
        {
            callback(result);
        }
    }];
}
```
['Protocol File for Checking for Updates and Calling Codes'](https://t.alipayobjects.com/L1/92/1059/1441875546401.zip)

