@cnName 1.5 FAQs
@priority 5

# 1.5 FAQs

[TOC]

I. Unable to send RPC requests on iOS9 platform 

On the iOS9 platform, Apple poses a restriction by default that applications must use the HTTPS protocol. That is why the requests sending will fail if the debugging gateway is of the HTTP protocol.  Solution: Add the following fields in the plist file of the application to allow the application to send HTTP requests. 
![g1](https://t.alipayobjects.com/images/rmsweb/T1aCXhXhNiXXXXXXXX.png)

II. How to add special header fileds for RPC requests

Usually, the HTTP header of RPC requests is added by the RPC module by default.  With any special header field existing in a certain RPC request, the special header field can be passed into the requestHeaderField dictionary at RPC invocation. 

```
/**
 * Excecute a RPC request according to the specified \code DTRpcMethod. 
 *
 * @param method An instance of the \code DTRpcCode type, describing the related information of the RPC request. 
 * @param params Parameters required by the RPC request. 
 * @param field  Head to be added to the request. 
 * @param responseBlock HeaderFields value requiring callback of response. 
 *
 * @return If the request is successful, return an object of the specified type. Otherwise, return nil. 
 */
- (id)executeMethod:(DTRpcMethod *)method params:(NSArray *)params requestHeaderField:(NSDictionary*)field responseHeaderFields:(void (^)(NSDictionary* allHeaderFields))responseBlock;
```

The RPC interceptor can achieve the same effect. In the next example, we demonstrate how to add fields to the header while calling the RPC request of 'rpc.operationA'. 

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

III. How to designate a special gateway for a RPC request?

After the global gateway of the application is configured, DTRpcConfig can be used to designate a special gateway for a RPC request. 

The codes below designate the RPC request 'alipay.client.saveUserProposalInfo' to go through a specified gateway URL. 

```
DTRpcConfig* config = [[[DTRpcClient defaultClient] configForScope:kDTRpcConfigScopeGlobal] copy];
feedbackConfig.gatewayURL = [NSURL URLWithString:@"https://mobilegw.alipay.com/mgw.htm"];
feedbackConfig.operationType = @"alipay.client.saveUserProposalInfo";
[[DTRpcClient defaultClient] setConfig:config forScope:kDTRpcConfigScopeGlobal];  
```
