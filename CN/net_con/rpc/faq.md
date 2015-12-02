@cnName 5 常见问题
@priority 5

# 5 常见问题

[TOC]

### 5.1 在 iOS9 上无法发送 RPC 请求 

因为 iOS9 上，苹果默认限制应用必须使用 https 协议，所以当调试网关为 http 时无法发送请求。解决方法为在应用的 plist 文件中添加如下字段，允许应用发送 http 请求。
![g1](https://t.alipayobjects.com/images/rmsweb/T1aCXhXhNiXXXXXXXX.png)

### 5.2 为 RPC 请求添加特殊的 Header 字段

通常，RPC 请求的 Http Header 是由 RPC 模块默认添加的。如果某条 RPC 请求有特殊的 Header 字段，可以在调用 RPC 时传入 requestHeaderField 字典。

```
/**
 * 根据指定的 \code DTRpcMethod 执行一个 RPC 请求。
 *
 * @param method 一个 \code DTRpcCode 类型的实例，描述了 RPC 请求的相关信息。
 * @param params RPC 请求需要的参数。
 * @param field  添加到 request 里面的 head
 * @param responseBlock 回调方法
 *
 * @return 如果请求成功，返回指定类型的对象，否则返回 nil。
 */
- (id)executeMethod:(DTRpcMethod *)method params:(NSArray *)params requestHeaderField:(NSDictionary*)field responseHeaderFields:(void (^)(NSDictionary* allHeaderFields))responseBlock;
```

使用 RPC 拦截器也可以实现，下面示例演示在调用`rpc.operationA`这个 RPC 请求时，向 Header 中添加字段。

```
@implementation HeaderFillRpcInterceptor

- (DTRpcOperation *)beforeRpcOperation:(DTRpcOperation *)operation
{
    if (![operation.rpcOperationType isEqualToString:@"rpc.operationA"])
    {
        NSMutableURLRequest* request = (NSMutableURLRequest*)operation.request;
        [request setValue:@"field" forHTTPHeaderField:"key"];
    }
    return operation;
}

@end
```

### 5.3 如何控制某个 RPC 请求走特殊的网关？

应用设置全局网关后，某个 RPC 请求需要走特殊网关时，可以通过 DTRpcConfig 来实现。

下面代码控制`alipay.client.saveUserProposalInfo`这个 RPC 请求走固定网关地址。

```
DTRpcConfig* config = [[[DTRpcClient defaultClient] configForScope:kDTRpcConfigScopeGlobal] copy];
feedbackConfig.gatewayURL = [NSURL URLWithString:@"https://mobilegw.alipay.com/mgw.htm"];
feedbackConfig.operationType = @"alipay.client.saveUserProposalInfo";
[[DTRpcClient defaultClient] setConfig:config forScope:kDTRpcConfigScopeGlobal];  
```

### 5.4 如何监控RPC耗时与错误

主站提供RPC耗时与流量监控功能，如图。
![image](https://os.alipayobjects.com/rmsportal/ybUvzoLawjOXukd.png)

这个功能由一个RPC拦截器负责，请下载下面代码，将RPC拦截器代码加入自己的工程中。并将这个拦截器添加到公共拦截器中使其生效。[代码下载](https://os.alipayobjects.com/others/APRPCLogInterceptor.zip)