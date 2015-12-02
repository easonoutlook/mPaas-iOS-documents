@cnName 5 Building Complete Demo
@priority 5

# 5 Building Complete Demo
[TOC]

This guide will instruct how to create an mPaaS-based application and how to configure the basic functions of client framework, RPC and logs.  In Section 3, we will create a demo application from scratch, including calling RPC interfaces, accessing Apple Push service and reporting crashes.  For more information about configuring and using modules, see the detailed documents of respective modules. 

---

## 5.1 Using mPaaS website

### 5.1.1 Creating your first application

After logging into the primary site with a developer account, the developer can see all the applications under the account. 
![main](https://os.alipayobjects.com/rmsportal/yXBpBmHJXaLQhOa.png)
Click "Add Application" to create a new application, and input the application name: 
![main1.5](https://os.alipayobjects.com/rmsportal/frMZxSXAsJxOmof.png)
![main2](https://os.alipayobjects.com/rmsportal/fbSQpXXihDAjyHV.png)
Select the mPaaS modules the application is dependent on (This step can be skipped. We recommend using mPaaS Xcode plugin to maintain modules.): 
![main3](https://os.alipayobjects.com/rmsportal/CSdkGhDuZaSKkFX.png)
After the setup is complete, the application you just created can be viewed on the Management Console. Click on the application and the application details will be displayed: 
![main5](https://os.alipayobjects.com/rmsportal/UGYjugFMrlfCVXB.png)

### 5.1.2 Generating mPaas.framework

___We recommend using mPaaS Xcode plugin to maintain modules. You do not need to re-compile mPaas.framework anymore.___

The developer can select mPaaS modules on the primary site to generate their own mPaas.framework.  Click "iOS Details" and select "Repackaging" on the pull-down page. The optional modules will be displayed. Check the desired modules, and click "OK". 
![framework1](https://os.alipayobjects.com/rmsportal/pEyVfTdezRMHgrK.png)

After packaging is complete, the developer can download the package, extract the mPaas.framework from the package and use it to replace the .framework file in the project: 
![framework2](https://os.alipayobjects.com/rmsportal/TuqSxGNDKlBrukH.png)

### 5.1.3 Building applications

The developer can select to use the online compiling feature provided by the primary site. First, the developer needs to upload the iOS packaging certificate, which is maintained by the developer, of the application. The certificate can be added or deleted. 
![build1](https://os.alipayobjects.com/rmsportal/jizbMpxeOYWJxKY.png)

Place the certificate (.p12) and the provisioning profile (.mobileprovision) to the same directory and generate a zip package.  In APP IDS field, fill in the corresponding Bundle Identifier for using the certificate. Packaging scripts will modify the Bundle ID of the application to this value.  In Certificate Password field, fill in the certificate key which will be used by the packaging tool for installing certificates automatically. 

Click Build->Add Build, fill in the required packaging options and certificate used, and start to package the application. 
![build2](https://os.alipayobjects.com/rmsportal/VEGiYvgqYkZjVCe.png)

After the build is complete, download the .ipa package of the application, or scan the QR code online to install the application. 

### 5.1.4 Viewing online status of application

When the application is accessed to the mPaaS, the developer can view its status on the primary site,  such as report-active logs, version distribution, crash rate and various monitoring indicators.  This feature will be constantly expanded to offer more valuable information for the developer. 
![monitor1](https://os.alipayobjects.com/rmsportal/vbBUxsMfREKMUgg.png)

## 5.2 Configuring mPaaS-based project

### 5.2.1 Creating application using command line tools or Xcode templates

By installing a command line tool or Xcode templates, the developer can quickly create an mPaaS-based project, saving the effort for the complicated project configurations.  (For details, see the ['Quick Start->Configuring Xcode Project'](./import/project_config.md) chapter.)
<br>
```
sh <(curl -s http://code.taobao.org/svn/mpaaskit/trunk/install.sh)
```
After the installation is complete, the developer can use the command 'mpaas createapp XXXX' to create a new application, or they can use the Xcode templates alternatively. 
![createapp](https://t.alipayobjects.com/images/rmsweb/T1lrxhXbdmXXXXXXXX.png)

### 5.2.2 Adding or removing module

The project created through the command line tool or Xcode templates is a simplest one, and it is integrated with only the most basic mPaaS modules by default.  To add or remove a module in future development, the developer can refer to the [1.2 Generating mPaas.framework](#1.2) chapter to generate the mPaas.framework on the primary site for replacing the existing .framework file in his/her own project. 
<br><br>
Attention: After a module is added, there may be a newly added system library or third-party dependency, or there may be a newly added *.bundle resource directory under the new mPaas.framework. They should also be added to the project. 
<br><br>
In the future, we will offer easier-to-use Xcode plug-ins to complete this job automatically. 

### 5.2.3 Initializing mPaaS

The mPaasInit method will be called to initialize mPaaS. (The project generated by templates already incorporates this method.): 
```
int main(int argc, char * argv[]) {
    @autoreleasepool {
        mPaasInit(@"TEST_IOS", [mPaasAppInterfaceImp class]);
        return UIApplicationMain(argc, argv, @"DFApplication", @"DFClientDelegate");
    }
}
```
The first argument is the 'mPaas appKey' of the application. It can be seen in the "iOS Details" on the primary site. 
![init1](https://t.alipayobjects.com/images/rmsweb/T1yI4hXXlXXXXXXXXX.png)

This AppKey and its secret key should be configured in Security Guard for signing RPC requests.  (If the access party does not require the RPC feature, this AppKey can be skipped.)

The second argument is the class name of the '@protocol mPaasAppInterface' implemented by the application.  Some arguments of mPaaS need to be provided by the application. The mPaaS module will call back methods of this interface.  For details, see ['Quick Start->Environment Variable and Configuration Service'](./import/env.md) chapters. 

### 5.2.4 Configuring applications and services of framework

The 'micro applications' and 'services' managed by the mPaaS client framework are configured in the MobileRuntime.plist file, as shown in the figure: 
![config](https://t.alipayobjects.com/images/rmsweb/T1kslhXkNeXXXXXXXX.png)
<br>
For specific how-tos, see ['Client Framework->Micro Application->Modifying Application Configuration'](./framework/application.md) and ['Client Framework->Service->Modifying Service Configuration'](./framework/Service.md) chapters. 

### 5.2.5 Configuring log module and crash reporting feature

The log module requires two arguments: 
<br>
<br>'Log Server Gateway': Log server URL of the application. 
<br>'Log Product Id': Log service product ID of the application. 
<br><br>
With the two callback methods under the '@protocol mPaasAppInterface', the two arguments can be returned to the mPaaS log module: 
<br>
```
- (NSString*)appRemoteLogServerURL;

- (NSString*)appRemoteLogProductId;
```

To enable crash reporting, the developer only needs to call the 'enable_crash_reporter_service()' method in the main function.   When the application uses Xcode for debugging on the simulator or a real machine, no crash logs will be reported. 

If your application uses the Setting Service provided by mPaaS, see ['Quick Start->Environment Variable and Configuration Service'](./import/env.md) chapters to configure the log module. 


### 5.2.6 Configuring RPC

With the callback method under the '@protocol mPaasAppInterface', the RPC gateway URL is returned to the mPaaS RPC module: 
<br>
```
- (NSString*)appRPCGatewayURL;
```
RPC interceptor is a way to achieve control on RPC behaviors on the client.  The application should use a Common Interceptor as the container class of all the interceptors. This Common Interceptor also implements '@protocol DTRpcInterceptor'.  All the self-defined interceptors should be added to the Common Interceptor.  ['Common Interceptor template code'](https://t.alipayobjects.com/L1/92/1072/1438761357756.zip)
<br><br>With the callback method under the '@protocol mPaasAppInterface', the Common Interceptor class name is returned to the mPaaS RPC module: 
<br>
```
- (NSString*)appRPCCommonInterceptorClassName
```
See the ['Basic Communication->RPC'](./net_con/rpc.md) chapter for more information. 

If your application uses the Setting Service provided by mPaaS, see the ['Quick Start->Environment Variable'](./import/env.md) chapter to configure the RPC module.

<span id="create_demo"></span>

## 5.3 Building demo application

### 5.3.1 Creating project

With the introduction above, now we will start to build a demo application with login, RPC request sending, APNS notification receiving, and crash reporting features. 

First, we will create a project with the command line tool. Let’s name the project SimpleDemoRpc,  and create the application on the primary site. 
![demo1](https://t.alipayobjects.com/images/rmsweb/T1.cthXo8dXXXXXXXX.png)

The demo project will integrate the RPC and the login window component of CommonUI, but no RPC or CommonUI modules can be found in the 'BuildConfig.json' file in mPaas.framework of the demo project.  So we need to go to the primary site, check MPHttpClient and MPCommonUI to repackage an mPaas.framework, and use it to replace the current mPaas.framework in the demo project. 

‘Note: after the framework file is replaced, you also need to add the APCommonUI.bundle into the project.  '

Run the project, but many symbols are not found.  This is because after the RPC module is introduced, we need to add some third-party dependencies. 
![demo3](https://t.alipayobjects.com/images/rmsweb/T1.ZdhXi4gXXXXXXXX.png)

Add 'SecurityGuardSDK' to the project.  The compiling succeeded.  ['Download Third-party Library'](./import/third.md)

Submit the codes to SVN and the repository URL can be found in the Basic Information area on the website: 
![demo4](https://os.alipayobjects.com/rmsportal/jXTTddJAhzRekuT.png)

### 5.3.2 Configuring mPaaS

Our demo project uses Setting Service for managing the environment. So we should make the 'appUseSettingService' method return YES: 
```
- (BOOL)appUseSettingService
{
    return YES;
}
```

The testing gateway URL:   http://10.218.157.194/mgw.htm<br>
the testing log server URL:   http://10.218.157.65<br>
log application ID:   SIMPLEDEMORPC_IOS-0000017768<br>
and Push application ID:   SIMPLEDEMORPC

are all configured into 'GatewayConfig.plist'. (The values are used by the demo project, and access parties will use their own configurations), as shown in the figure: 
![demo5](https://t.alipayobjects.com/images/rmsweb/T1eINhXcJdXXXXXXXX.png)

### 5.3.3 Adding login and APNS Token accessing features

The login feature is abstracted into the micro application 'LoginAppDelegate' and login service 'LoginService',   and added to 'MobileRuntime.plist': 
![demo6](https://t.alipayobjects.com/images/rmsweb/T1pIBhXXXfXXXXXXXX.png)

'LoginService' is in charge of obtaining the APNS Token of the application, open account and password login interface (this interface is exclusive to the demo application), as well as recording the sessionId of successful logins: 
```
@interface LoginServiceImpl ()
{
    NSString* _sessionId;
    NSString* _pushToken;
}

@end

@implementation LoginServiceImpl

@synthesize sessionId = _sessionId;
@synthesize pushToken = _pushToken;

- (void)start
{
    NSLog(@"LoginServiceImpl started");
    
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

- (NSString*)loginWithUserName:(NSString*)userName password:(NSString*)password
{
    id<MPSettingService> settings = [mPaas() serviceWithName:@"SettingService"];
    NSString* appCode = settings.settings[@"mPaasPushAppId"];
    appCode = appCode ?: @"";
    
    MPAASGWLoginDemoServiceFacade* facade = [[MPAASGWLoginDemoServiceFacade alloc] init];
    _sessionId = [facade login:@{@"userId":userName, @"password":password, @"appCode":appCode, @"token":_pushToken ?: @"", @"osType":@"2"}];
    
    NSLog(@"sessionId:%@", _sessionId);
    return _sessionId;
}

@end
```
When the login service is started, 'LoginService' requests the APNS Token from the system, and monitors the framework notification of 'UIApplicationDidRegisterForRemoteNotifications'. The obtained APNS Token will be logged into _pushToken. 

The 'loginWithUserName:password:' interface receives the account and password, and invokes RPC login requests. The 'appCode' indicates the Push service ID which is available in the Setting Service of mPaaS.  The 'osType' is 2, indicating that it is an iOS client. Upload “_pushToken” and bind the token to the user name in the background. 

Add codes in the login window to call the LoginService interface. After successful login, synchronize the user's login status to the log service and Data Center. For details, see [User-mode Processing](./import/user.md). 
```
__block BOOL succeed = YES;
__weak DMLoginBoxViewController* weakSelf = self;
NSString* userName = _loginBox.usernameField.text;
NSString* password = _loginBox.passwordField.text;
_loginCaller = [self callAsyncBlock:^{
    @try
    {
        id<LoginService> service = [DTContextGet() findServiceByName:@"LoginService"];
        [service loginWithUserName:userName password:password];
    }
    @catch (DTRpcException *exception)
    {
        succeed = NO;
    }
} completion:^{
    DMLoginBoxViewController* strongSelf = weakSelf;
    strongSelf->_loginCaller = nil;
    if (succeed)
    {
        [[APDataCenter defaultDataCenter] setCurrentUserId:userName];
        [[APMonitorPointManager sharedInstance] setAuthenticated:YES];
        [[APMonitorPointManager sharedInstance] setUserId:userName];
        
        [APRemoteLogger writeLogWithActionId:KActionID_Event extParams:@[userName] appId:@"" seed:@"login" ucId:@""];
        
        [[NSNotificationCenter defaultCenter] postNotificationName:LOGIN_SUCCESS_NOTIFICATION object:userName];
        [[DTContextGet() findApplicationByName:@"20000003"] exitAnimated:YES];
    }
    else
    {
        [strongSelf showAlert:[NSString stringWithFormat:@"Login failed"]]; 
    }
}];
```

### 5.3.4 Adding RPC interceptor to simulate the action of sending RPC requests for login status

After login, we can send the RPC request requiring the login status.  To this end, we make a convention that the sessionId obtained after the login should be placed in the RPC request header.  (In usual cases, the client software is implemented through HTTP Cookie. Here we make it simpler.)

Add a self-defined RPC interceptor 'FCHeaderSessionRpcInterceptor'.  This interceptor will intercept all the RPC requests. If it is not a login request, the sessionId field will be added into the header. 
```
@interface FCHeaderSessionRpcInterceptor : NSObject <DTRpcInterceptor>
@end
```
```
#import "LoginService.h"
@implementation FCHeaderSessionRpcInterceptor

- (DTRpcOperation *)beforeRpcOperation:(DTRpcOperation *)operation
{
    if (![operation.rpcOperationType isEqualToString:@"alipay.mpaasgw.logindemo"])
    {
        // Place the session into the header. 
        id<LoginService> service = [DTContextGet() findServiceByName:@"LoginService"];
        if (service.sessionId)
        {
            NSMutableURLRequest* request = (NSMutableURLRequest*)operation.request;
            [request setValue:[service.sessionId copy] forHTTPHeaderField:@"sessionId"];
        }
    }
    return operation;
}
@end
```
Add 'FCHeaderSessionRpcInterceptor' to the RPC interceptor container class. 
```
@implementation DTRpcCommonInterceptor

- (id)init
{
	self = [super init];
	if (self)
    {
        NSMutableArray *interceptorList = [[NSMutableArray alloc]init];
        FCHeaderSessionRpcInterceptor *hsInterceptor = [[FCHeaderSessionRpcInterceptor alloc] init];
        [interceptorList addObject:hsInterceptor];
        self.interceptorArray = interceptorList;
	}
	return self;
}

@end
```
Notify RPC server of the interceptor container class name in mPaasAppInterfaceImp callback. 
```
- (NSString*)appRPCCommonInterceptorClassName
{
    return @"DTRpcCommonInterceptor";
}
```
As gzip is not enabled on the testing gateway, we should disable gzip compression on RPC.  This piece of code can be placed under the 'appDelegateEvent:arguments:' method of mPaasAppInterfaceImp, and implemented in 'mPaasAppEventAfterDidFinishLaunching' events. 
```
// The testing gateway does not support gzip compression. 
DTRpcConfig* demoRpcConfig = [[[DTRpcClient defaultClient] configForScope:kDTRpcConfigScopeGlobal] copy];
demoRpcConfig.requestGZip = NO;
[[DTRpcClient defaultClient] setConfig:demoRpcConfig forScope:kDTRpcConfigScopeGlobal];
```

Next, we will add codes to simulate the action of sending a RPC request. 
```
__block NSString* result = nil;
[self callAsyncBlock:^{
    @try
    {
        DTRpcMethod *method = [[DTRpcMethod alloc] init];
        method.operationType = @"alipay.mpaasgw.needlogintest";
        method.checkLogin = NO;
        method.returnType = @"@\"NSString\"";
        method.signCheck = YES;
        
        result = [[DTRpcClient defaultClient] executeMethod:method params:nil callback:^(DTRpcOperation *operation) {
            NSLog(@"%@", operation);
        }];
    }
    @catch(DTRpcException *exception)
    {
        result = exception.reason;
    }
} completion:^{
    [self showAlert:result];
}];
```

### 5.3.5 Developing interfaces and adding codes simulating crashes

First, enable the crash reporting feature in the main function. 
```
int main(int argc, char * argv[]) {
	enable_crash_reporter_service();
    @autoreleasepool {
        mPaasInit(@"SIMPLEDEMORPC_IOS", [mPaasAppInterfaceImp class]);
        return UIApplicationMain(argc, argv, @"DFApplication", @"DFClientDelegate");
    }
}
```
Then, add a piece of code simulating the crash. 
```
- (void)raiseOutOfRangeException
{
    NSArray * array = @[@"Hello", @"World"];
    NSString * str = [array objectAtIndex:2];
    NSLog(@"String value:%@", str);
}
```

OK. Our demo project has been complete. Now upload the codes to SVN. 

### 5.3.6 Testing demo application

First, upload the certificate used for the packaging (a zip package containing the certificate (.p12) and the provisioning profile (.mobileprovision) files). 
![build1](https://os.alipayobjects.com/rmsportal/uXFTTGvfTMDqVzX.png)

Upload the certificate (.p12) for Apple Push service. 
![build2](https://os.alipayobjects.com/rmsportal/dSZaVnPtpQfEljH.png)

After the build, install the demo application to a mobile phone and log in using the 'alipayAdmin' account (Password: ali123).  After successful login, click to send a RPC request and you will see a prompt returned: "Welcome to mPaaS".<br>
![build3](https://t.alipayobjects.com/images/rmsweb/T1JZxhXixfXXXXXXXX.PNG)

On the 'Development on Cloud->Push->Push Notification' interface, push a message to the login user 'alipayAdmin'. 
![build4](https://os.alipayobjects.com/rmsportal/HJExYygmqDrgfVF.png)
The message is received successfully. <br>
![build5](https://t.alipayobjects.com/images/rmsweb/T1lb4hXnhjXXXXXXXX.PNG)

Click "Make a Crash" to simulate a crash of the demo application. Restart the demo application to upload the crash report to the server.  Two minutes later, we will see the crash log on the website. <br>

[Download complete codes of demo application](https://t.alipayobjects.com/L1/92/1072/1438847775126.zip)

