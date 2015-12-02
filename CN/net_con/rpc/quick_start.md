@cnName 3 快速开始
@priority 3

# 3 快速开始

[TOC]

## 3.1 RPC 模块

RPC 模块为 MPHttpClient，里面的关键类功能如下：

|类|功能|
|-|-|
| DTRpcClient | RPC 客户端管理类，为单例。负责发送 RPC 请求，处理 RPC 请求配置。|
| DTRpcConfig | RPC 配置，指定 RPC 的网关、缓存策略、是否使用 GZip 等。可以控制每个配置的影响范围。|
| DTRpcMethod | RPC 请求方法描述，记录 RPC 请求的方法名、参数、返回类型等信息。|
| DTRpcOperation | RPC 请求操作实体，为 NSOperation 子类。|
| DTRpcAsyncCaller | RPC 异步调用器，提供异步调用 RPC 的快捷接口。|

## 3.2 网关配置

应用在 mPaasAppInterface 接口的实现中，通过下面的接口返回网关地址。如果使用系统设置服务，可以将 RPC 网关地址配置在 GatewayConfig.plist 中。
```C
#pragma mark 网络配置

/**
 *  RPC 的服务器地址
 */
- (NSString*)appRPCGatewayURL;

/**
 *  RPC 的 ETag 服务器地址
 */
- (NSString*)appRPCETagURL;
```

更多信息请参考[使用系统设置服务](../../appendix/settings.md)

## 3.3 发 RPC 请求

RPC 请求必须在子线程调用，同时回调方法默认为主线程。

-  使用 DTRpcAsyncCaller

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

-  使用框架内 DTViewController

下面例子为在 DTViewController 的子类 ViewController 的方法里发送 RPC 请求。
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