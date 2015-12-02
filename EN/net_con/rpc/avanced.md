@cnName 1.4 Guide to Advanced Features
@priority 4

# 1.4 Guide to Advanced Features

[TOC]

## 1.4.1 Using RPC interceptor 


### 1.4.1.1 Working principle of RPC interceptor

Below are the simplified codes of DTRpcClient for executing RPC operations.  Before and after the request is sent, the DTRpcOperation object will be passed to every interceptor for processing.  With no processing required, the interceptor can simply let the object pass. 

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

### 1.4.1.2 Interface protocol of RPC interceptor

```
/**
 * Interceptor of RPC requests.  All the RPC requests, before being sent to the server or after receiving responses from the server, 
 * will pass through the interceptor which will process the request accordingly. 
 */
@protocol DTRpcInterceptor <NSObject>

@optional

/**
 * Pre-interceptor of RPC requests. This method will be called before the RPC request starts.
 *
 * @param operation Current RPC request. 
 */
- (DTRpcOperation *)beforeRpcOperation:(DTRpcOperation *)operation;

/**
 * Post-interceptor of RPC requests. This method will be called after the RPC request ends.
 *
 * @param operation Current RPC request.
 */
- (DTRpcOperation *)afterRpcOperation:(DTRpcOperation *)operation;

- (void)handleException:(NSException *)exception;

@end
```

- To register a RPC interceptor container

	From the codes above, we can see that DTRpcClient will only log one interceptor object, but the application may have multiple interceptors processing RPC requests together.  Here we can create an interceptor container implementing 'beforeRpcOperation' and 'afterRpcOperation' interfaces to dispatch events to all of its child interceptors. 
    
	This interceptor container should be added to the project by the developer. The class name should be returned in the method under mPaasAppInterface and RPC module will create objects for the container automatically. 

	```
	- (NSString*)appRPCCommonInterceptorClassName
	{
    	return @"DTRpcCommonInterceptor";
	}
	```

	RPC interceptor is a way to achieve control on RPC behaviors on the client. The application should use a Common Interceptor as the container class of all the interceptors. This Common Interceptor also implements '@protocol DTRpcInterceptor'. All the self-defined interceptors should be added to the Common Interceptor.

	['Common Interceptor template code'](https://t.alipayobjects.com/L1/92/1072/1438761357756.zip)

-  Basic error processing of RPC interceptors

	The container interceptor cannot process any errors. The developer needs to connect their own interceptor for processing network errors.  The Base Interceptor template provided below can process network errors and session expiration errors.  The developer can modify it as needed and integrate it into the project code.  Add this DTRpcBaseInterceptor to the interceptor container and it will be ready for use. 

	['Base Interceptor template code'](https://t.alipayobjects.com/L1/92/1066/1441858237059.zip)


## 1.4.2 RPC Request signature

-  Signature logic<br><br>

	RPC requests have a signature mechanism to protect client requests from being tampered or falsified. The sign-check is done by mPaaS automatically.  The basic signing method is to generate a string for the content in the requestBody, and encrypt the string with the key saved in the Security Guard.  The signature is contained in the request and sent to the gateway. The gateway will sign the request with the same approach and verify whether the two signatures are consistent. 
<br><br>
-  To manage request sign-check<br><br>

	The signCheck property in the DTRpcMethod class specifies whether to sign the request or not: 
	```
	/** Whether to sign */
	@property(nonatomic, assign) BOOL signCheck;
	```

	In usual cases, all the RPC requests of the application require signature, which can be achieved through the RPC interceptor.  Sample codes: 

	```
	@implementation RPCDefaultSignCheckInterceptor

	- (DTRpcOperation *)beforeRpcOperation:(DTRpcOperation *)operation
	{
    	operation.method.signCheck = YES;
	}

	@end
	```

-  To save signature key in Security Guard<br><br>

	The signature key used by the client is saved in Security Guard. You may use Xcode plugin of mPaaS Developer Tools to generate your own secure data.

	When the application is generating a key file in the Security Guard, it creates an item in Appkey field, and the value is the AppKey of the application.  The Appkey here must be consistent with the key used in mPaaS initialization in the mPaasInit method.  The RPC module will use the key matched with this Appkey to sign the request. 

  ```
  int main(int argc, char * argv[]) {
      enable_crash_reporter_service();
      @autoreleasepool {
          mPaasInit(@"THFUND_IOS", [mPaasAppInterfaceImp class]);
          return UIApplicationMain(argc, argv, @"DFApplication", @"DFClientDelegate");
      }
  }
  ```

