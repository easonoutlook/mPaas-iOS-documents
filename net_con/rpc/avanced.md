@cnName 4 进阶指南
@priority 4

# 4 进阶指南

[TOC]

## 4.1 使用 RPC 拦截器


### 4.1.1 RPC 拦截器工作原理

下面是简化的 DTRpcClient 执行 RPC 操作的代码。在请求发送前与发送后，会将这个 DTRpcOperation 对象传给每个 interceptor 进行处理。拦截器不需要处理时直接放过放过即可。

```
- (id)executeOperation:(DTRpcOperation *)operation responseHeaderFields:(void (^)(NSDictionary* allHeaderFields))responseBlock
{
    if (self.interceptor) {
        [self.interceptor beforeRpcOperation:operation];
    }
    
    [self.requestQueue addOperation:operation];
    [operation waitUntilFinished];
    
    if (self.interceptor) {
        operation = [self.interceptor afterRpcOperation:operation];
    }
    
    if (operation.error) {
        [DTRpcException raise:(DTRpcErrorCode)operation.error.code message:operation.error.localizedDescription userInfo:dict];
    }
    
    return operation.resultObject;
}
```

### 4.1.2 RPC 拦截器接口协议

```
/**
 * 对 RPC 请求的拦截器。所有的 RPC 请求，在发送到服务端之前或从服务端接收到回应之后，
 * 都会经过拦截器，拦截器可以做相应的处理。
 */
@protocol DTRpcInterceptor <NSObject>

@optional

/**
 * RPC 请求的前置拦截器。在 RPC 请求开始之前，会调用该方法。
 *
 * @param operation 当前的 PRC 请求。
 */
- (DTRpcOperation *)beforeRpcOperation:(DTRpcOperation *)operation;

/**
 * RPC 请求后置拦截器。在 PRC 请求结束之后，会调用该方法。
 *
 * @param operation 当前的 RPC 请求。
 */
- (DTRpcOperation *)afterRpcOperation:(DTRpcOperation *)operation;

- (void)handleException:(NSException *)exception;

@end
```

- 注册 RPC 拦截器容器

从上面代码看到，DTRpcClient 只会记录一个拦截器对象，但是应用可能有多个拦截器一起处理 RPC 请求。这里我们可以创建一个拦截器容器，让这个容器实现`beforeRpcOperation`与`afterRpcOperation`接口，并将事件分发给自己的所有子拦截器。

这个拦截器容器需要开发者添加到自己的工程中，并在 mPaasAppInterface 的下面方法里将类名返回，RPC 模块会自动创建这个容器对象。

```
- (NSString*)appRPCCommonInterceptorClassName
{
    return @"DTRpcCommonInterceptor";
}
```

RPC 拦截器是实现客户端控制 RPC 行为的方式。应用应该使用一个 Common Interceptor 来作为所有拦截器的容器类，这个 Common Interceptor 本身也实现`@protocol DTRpcInterceptor`。所有自定义的拦截器，请添加到这个 Common Interceptor 中。

[`Common Interceptor 模板代码`](https://t.alipayobjects.com/L1/92/1072/1438761357756.zip)

-  基本错误处理 RPC 拦截器


容器拦截器并不能处理任何错误，开发者需要接入自己的拦截器处理网络错误。下面提供的这个 Base Interceptor 模板，可以处理网络错误，与 Session 失效的错误。开发者根据应用需要进行修改，并集成到自己的工程代码里。将这个 DTRpcBaseInterceptor 添加到拦截器容器中，即可使用。

[`Base Interceptor 模板代码`](https://t.alipayobjects.com/L1/92/1066/1441858237059.zip)


## 4.2 RPC 请求签名

-  签名逻辑

为保证客户端请求不被篡改和伪造，RPC 请求有签名机制，加签会自动完成。基本签名方法为对 requestBody 中的内容生成串，并使用保存在无线保镖中的加密密钥进行加密。将签名放在请求中发给网关，网关使用相同方式签名，校验两个签名是否相等。

-  控制请求加签

DTRpcMethod 类中的 signCheck 属性指定该请求是否需要加签：
```
/** 是否签名 */
@property(nonatomic, assign) BOOL signCheck;
```

但通常，应用中的所有 RPC 请求都需要签名，这可以通过 RPC 拦截器实现。示例代码：

```
@implementation RPCDefaultSignCheckInterceptor

- (DTRpcOperation *)beforeRpcOperation:(DTRpcOperation *)operation
{
    operation.method.signCheck = YES;
}

@end
```

-  使用无线保镖保存签名密钥

客户端使用的签名密钥保存在无线保镖中，如图：
![g1](https://t.alipayobjects.com/images/rmsweb/T1I84hXa8jXXXXXXXX.png)

应用在生成无线保镖密钥文件时，在 Appkey 栏目里创建项目，Key 值为自己的应用 Appkey。这个 Appkey 与 mPaasInit 方法里初始化的 Key 必须保持一致。RPC 模块会使用这个 Appkey 对应的密钥来对请求进行加签。

```
int main(int argc, char * argv[]) {
    enable_crash_reporter_service();
    @autoreleasepool {
        mPaasInit(@"THFUND_IOS", [mPaasAppInterfaceImp class]);
        return UIApplicationMain(argc, argv, @"DFApplication", @"DFClientDelegate");
    }
}
```