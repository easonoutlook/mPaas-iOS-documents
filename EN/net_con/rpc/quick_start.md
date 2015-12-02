@cnName 1.3 Quick Start
@priority 3

# 1.3 Quick Start

[TOC]

## 1.3.1 RPC module

The RPC module in mPaaS is MPHttpClient. The key classes and functions it contains include the following: 

|Class|Function|
|-|-|
| DTRpcClient | RPC client management class, singleton. It sends RPC requests and handles RPC request configurations.  |
| DTRpcConfig | RPC confutations, including specifying the RPC gateway, cache policy and whether to use GZip.  It can control the influence scope of every configuration item.  |
| DTRpcMethod | Descriptions of RPC request methods. It records the method name, arguments and return types of RPC requests.  |
| DTRpcOperation | Operating entity of RPC requests, a subclass of NSOperation.  |
| DTRpcAsyncCaller | RPC asynchronous caller. It provides a quick interface for asynchronous RPC calls.  |

## 1.3.2 Gateway configuration

In implemention of mPaasAppInterface interfaces, the application returns the gateway URL using the interface below.  If the system Setting Service is enabled, the RPC gateway URL can be configured in GatewayConfig.plist. 
```C
#pragma mark Network configurations

/**
 *  RPC server URL
 */
- (NSString*)appRPCGatewayURL;

/**
 *  ETag server URL of the RPC service
 */
- (NSString*)appRPCETagURL;
```

For more information, see [Using Setting Service](../../appendix/settings.md)

## 1.3.3 Sending RPC request

The RPC request must be called in the subthread, while the callback method is in the main thread by default. 

-  Use DTRpcAsyncCaller

```C
__block ALPRSAPKeyResult* result = nil;
[DTRpcAsyncCaller callAsyncBlock:^{
    @try
    {
        DTRpcMethod *method = [[DTRpcMethod alloc] init];
        method.operationType = @"alipay.client.getRSAKey";
        method.checkLogin =  NO ;
        method.returnType = @"@\"ALPRSAPKeyResult\"" ;
        method.signCheck = YES;
        
        result = [ [DTRpcClient defaultClient] executeMethod:method params:nil];
    }
    @catch (NSException *exception) {
        NSLog(@"%@", exception);
    }
} completion:^{
    NSLog(@"%@", result);
}];
```

-  Use DTViewController in the framework

In the example below, the RPC request is sent in the method of the ViewController subclass of the DTViewController. 
```C
__block ALPRSAPKeyResult* result = nil;
[self callAsyncBlock:^{
    @try
    {
        DTRpcMethod *method = [[DTRpcMethod alloc] init];
        method.operationType = @"alipay.client.getRSAKey";
        method.checkLogin =  NO ;
        method.returnType =   @"@\"ALPRSAPKeyResult\"" ;
        method.signCheck = YES;
        
        result = [ [DTRpcClient defaultClient] executeMethod:method params:nil];
    }
    @catch (NSException *exception) {
        NSLog(@"%@", exception);
    }
} completion:^{
    NSLog(@"%@", result);
}];
```
