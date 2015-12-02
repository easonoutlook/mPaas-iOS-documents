@cnName 5 搭建一个完整 Demo
@priority 5

# 5 搭建一个完整 Demo
[TOC]

本指南指导如何创建基于移动的应用，并配置客户端框架、RPC、日志等基础功能。第三部分会从零开始创建一个 Demo，包括调用 RPC 接口，使用苹果 Push，进行 Crash 上报等。更多模块的配置使用方法，请参考各模块的详细文档。

---

## 5.1 使用移动主站

### 5.1.1 创建第一个应用

使用开发者账号登录主站后，开发者可以看到自己账号下的所有应用。
![main](https://t.alipayobjects.com/images/rmsweb/T1ZslhXa0eXXXXXXXX.png)
点击“添加应用”新建一款应用，输入应用的名称：
![main2](https://t.alipayobjects.com/images/rmsweb/T1lIxhXlRaXXXXXXXX.png)
然后选择应用依赖的移动模块，这一步可以先跳过：
![main3](https://t.alipayobjects.com/images/rmsweb/T11IJhXcpbXXXXXXXX.png)
完成后可以在管理面板看到刚才创建的应用，点击进入应用详情：
![main5](https://t.alipayobjects.com/images/rmsweb/T16sJhXaVbXXXXXXXX.png)

### 5.1.2 生成 mPaas.framework

开发者可以在主站选择移动模块，生成自己的 mPaas.framework。点击 iOS 详情，在展开的下拉页面中选择“重新打包”，可以看到可选的模块，勾选后点击确认。
![framework1](https://t.alipayobjects.com/images/rmsweb/T1hcNhXXtbXXXXXXXX.png)

打包完成后，开发者可以自行下载压缩包，解压出 mPaas.framework 替换到工程中：
![framework2](https://t.alipayobjects.com/images/rmsweb/T1PrJhXbXkXXXXXXXX.png)

### 5.1.3 构建应用

开发者可以选择使用主站提供的在线编译功能，首先需要上传应用的 iOS 打包证书（证书由开发者自行维护），证书可以添加、删除进行管理。
![build1](https://t.alipayobjects.com/images/rmsweb/T17r8hXd8gXXXXXXXX.png)

请将证书 p12 文件，与 mobileprovision 扩展名的预置描述文件放在同一目录下，生成 zip 格式压缩包。APP IDS 填写使用这个证书对应的 Bundle Identifier，打包脚本在打包时会把应用的 Bundle Id 修改为该值。Certificate Password 为证书的密钥，打包机自动化安装证书时会使用到。

点击构建->添加构建，填写需要的打包选项和使用的证书，就可以开始打包应用了。
![build2](https://t.alipayobjects.com/images/rmsweb/T1jY4hXeBhXXXXXXXX.png)

构建完成可以下载应用的 ipa 包，或者在线扫描二维码安装。

### 5.1.4 查看应用线上状态

应用接入移动后，开发者可以在主站上在线查看应用的状态。比如：报活量、版本分布、Crash 率，各项监控指标等。这部分功能会不断丰富，为开发者提供更多有价值的信息。
![monitor1](https://t.alipayobjects.com/images/rmsweb/T1tY0hXoJhXXXXXXXX.png)

## 5.2 配置基于移动的工程

### 5.2.1 使用命令行工具或 Xcode 模板创建应用

安装命令行工具或 Xcode 模板可以快速创建基于移动的工程，省去复杂的工程配置工作。（具体可以参考[接入指南->配置 Xcode 工程](../appendix/project_config.html)章节）
<br>
```
sh <(curl -s http://code.taobao.org/svn/mpaaskit/trunk/install.sh)
```
安装完成后使用命令行`mpaas createapp XXXX`创建新应用；或者使用 Xcode 模板
![createapp](https://t.alipayobjects.com/images/rmsweb/T1lrxhXbdmXXXXXXXX.png)

### 5.2.2 添加或删除模块

使用命令行工具或模板创建的工程为最简工程，默认只携带了最基本的移动模块。开发者在日后的开发工作中需要增减模块时，可以参考 5.1.2 生成 mPaas.framework 章节，去主站上重新生成 mPaas.framework，替换到自己的工程中。
<br><br>
`注意`增加模块后，可能有新增的系统库，或第三方库依赖，或新的 mPaas.framework 下面可能有新增的*.bundle 资源目录，也需要添加到工程中。
<br><br>
未来，我们会提供更简单易用的 Xcode 插件，自动完成这些工作。

### 5.2.3 初始化

移动框架的初始化需要调用`mPaasInit`方法（用模板生成的工程已经添加该方法）：
```
int main(int argc, char * argv[]) {
    @autoreleasepool {
        mPaasInit(@"TEST_IOS", [mPaasAppInterfaceImp class]);
        return UIApplicationMain(argc, argv, @"DFApplication", @"DFClientDelegate");
    }
}
```
其中第一个参数为应用的`mPaas appKey`，可以在主站上选择 iOS 应用详情里看到：
![init1](https://t.alipayobjects.com/images/rmsweb/T1yI4hXXlXXXXXXXXX.png)

这个 appKey 与其对应的密钥，需要配置在无线保镖中，用于 RPC 请求的加签。（如果接入方不使用 RPC 功能，这个 appKey 也可以不传）

第二个参数是应用实现的`@protocol mPaasAppInterface`接口的类名。有一些参数需要应用提供，移动模块会回调这个接口的方法。详情请参考[`实现mPaasAppInterface`](./env.md)章节。

### 5.2.4 配置框架应用与服务

移动客户端框架管理的`微应用`与`服务`，配置在 MobileRuntime.plist 文件中，如图：
![config](https://t.alipayobjects.com/images/rmsweb/T1kslhXkNeXXXXXXXX.png)
<br>
具体配置方式请参考[`客户端框架->快速开始`](../framework/quick_start.md)章节。

### 5.2.5 配置日志模块与 Crash 上报功能

日志模块需要两个参数：
<br>
<br>`Log Server Gateway`：应用的日志服务器地址
<br>`Log Product Id`：应用的日志服务产品 ID
<br><br>
这两个参数可以使用`@protocol mPaasAppInterface`接口的下面两个回调方法返回给移动日志模块：
<br>
```
- (NSString*)appRemoteLogServerURL;

- (NSString*)appRemoteLogProductId;
```

开启 Crash 上报功能只需要在 main 函数中调用`enable_crash_reporter_service()`方法即可。当应用使用 Xcode 在模拟器或真机调试时，不会上报 Crash 日志。

如果你的应用使用了`Setting Service`请参阅[`接入指南->使用系统设置服务`](./settings.md)章节来配置日志模块。


### 5.2.6 配置 RPC

RPC 网关地址使用`@protocol mPaasAppInterface`接口的下面这个回调方法返回给移动 RPC 模块：
<br>
```
- (NSString*)appRPCGatewayURL;
```
RPC 拦截器是实现客户端控制 RPC 行为的方式。应用应该使用一个 Common Interceptor 来作为所有拦截器的容器类，这个 Common Interceptor 本身也实现`@protocol DTRpcInterceptor`。所有自定义的拦截器，请添加到这个 Common Interceptor 中。[`Common Interceptor 模板代码`](https://t.alipayobjects.com/L1/92/1072/1438761357756.zip)
<br><br>将 Common Interceptor 的类名在`@protocol mPaasAppInterface`接口的下面这个回调方法中返回给移动 RPC 模块：
<br>
```
- (NSString*)appRPCCommonInterceptorClassName
```
参考[`基础通信->RPC->快速开始`](../net_con/rpc/quick_start.md)章节获取更多信息。

如果你的应用使用了`Setting Service`请参阅[`接入指南->使用系统设置服务`](./settings.md)章节来配置 RPC 模块。

<span id="create_demo"></span>

## 5.3 动手搭建一个 Demo

### 5.3.1 创建工程

有了上面的介绍，我们现在开始搭建一个拥有登录，发RPC 请求，接收 APNS，上报 Crash 功能的 Demo。

首先我们用命令行工具创建一个工程，叫 SimpleDemoRpc 好了。同时到主站上创建我们的应用。
![demo1](https://t.alipayobjects.com/images/rmsweb/T1.cthXo8dXXXXXXXX.png)

我们的 Demo 要使用 RPC 以及 CommonUI 里的登录窗组件，但是打开 Demo 工程的 mPaas.framework 里面的`BuildConfig.json`文件，发现没有 RPC 和 CommonUI 模块。所以去主站上勾选MPHttpClient、MPCommonUI 重新打一个 mPaas.framework 替换进来。

`注意，替换 framework 后，也要把 APCommonUI.bundle 加到工程里。`

运行一下我们的工程，好多符号找不到。这是因为引入 RPC 模块后，需要添加一些第三方依赖。
![demo3](https://t.alipayobjects.com/images/rmsweb/T1.ZdhXi4gXXXXXXXX.png)

把`无线保镖（ SecurityGuardSDK）`添加到工程里。编译通过！[`三方库下载`](./attach.md)

把代码提交到 svn 上，svn 地址可以在主站应用详情里看到：
![demo4](https://t.alipayobjects.com/images/rmsweb/T17sBhXgJdXXXXXXXX.png)

### 5.3.2 配置

我们的 Demo 使用 Setting Service 来管理环境，所以让`appUseSettingService`这个方法返回 YES：
```
- (BOOL)appUseSettingService
{
    return YES;
}
```

测试网关地址： http://10.218.157.194/mgw.htm<br>
测试日志服务器地址： http://10.218.157.65<br>
日志应用 ID： SIMPLEDEMORPC_IOS-0000017768<br>
Push 应用 ID： SIMPLEDEMORPC

都配置到`GatewayConfig.plist`里（这些值为 Demo 使用，接入方 App 会使用自己的配置），如图：
![demo5](https://t.alipayobjects.com/images/rmsweb/T1eINhXcJdXXXXXXXX.png)

### 5.3.3 添加登录与获取APNS Token 功能

登录功能抽象成登录的微应用`LoginAppDelegate`与登录服务`LoginService`。添加到`MobileRuntime.plist`中：
![demo6](https://t.alipayobjects.com/images/rmsweb/T1pIBhXXXfXXXXXXXX.png)

登录服务负责获取应用的 APNS Token，公开账密登录接口（该接口仅为 Demo 使用），并且在登录成功后，记录该次登录的 sessionId：
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
    
    // 注册推送
#ifdef __IPHONE_8_0    //ios8 注册推送
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
登录服务启动时，向系统请求获取APNS Token，并监听框架的通知`UIApplicationDidRegisterForRemoteNotifications`，在获取到 APNS Token 后记录到_pushToken 中。

`loginWithUserName:password:`这个接口接收账号、密码，调用登录 RPC 请求，`appCode`是 Push 应用 ID，可以从 SettingService 里获取。`osType`传 2，表示这是 iOS 客户端，同时上传_pushToken 让后台将这个 Token 与用户名绑定。

在登录窗添加代码调用 LoginService 的接口，并在登录成功后，向日志系统和统一存储同步用户登录态，具体可参考[`用户态处理`](./attach.md)
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
        [strongSelf showAlert:[NSString stringWithFormat:@"登录失败"]];
    }
}];
```

### 5.3.4 添加 RPC 拦截器，模拟发送需要登录态的 RPC 请求

登录完成后，我们可以发送需要登录态的 RPC 请求。为了模拟这种情况，我们约定将登录完成后获取的 sessionId 放在 RPC 请求的头里。（通常客户端软件是使用 Http Cookie 来实现，这里我们简化一下）

添加一个自定义的 RPC 拦截器，FCHeaderSessionRpcInterceptor。这个拦截器截获所有 RPC 请求，如果不是登录请求，那么就在 Header 里填写 sessionId 字段。
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
        // 把 session 放到 header 里
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
在我们的 RPC 拦截器容器类里添加 FCHeaderSessionRpcInterceptor
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
在 mPaasAppInterfaceImp 的回调中告诉 RPC 我们的拦截器容器类名
```
- (NSString*)appRPCCommonInterceptorClassName
{
    return @"DTRpcCommonInterceptor";
}
```
因为测试网关没有开启 gzip 压缩，所以我们全局设置一下 RPC，不使用 gzip 压缩。这段代码可以放在 mPaasAppInterfaceImp 的`appDelegateEvent:arguments:`方法下，实现在`mPaasAppEventAfterDidFinishLaunching`事件中。
```
// 测试网关不支持 gzip
DTRpcConfig* demoRpcConfig = [[[DTRpcClient defaultClient] configForScope:kDTRpcConfigScopeGlobal] copy];
demoRpcConfig.requestGZip = NO;
[[DTRpcClient defaultClient] setConfig:demoRpcConfig forScope:kDTRpcConfigScopeGlobal];
```

下面我们添加代码模拟发送一个 RPC 请求
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

### 5.3.5 开发界面并添加模拟 Crash 的代码

在 main 函数中开启 Crash 上报功能
```
int main(int argc, char * argv[]) {
    enable_crash_reporter_service();
    @autoreleasepool {
        mPaasInit(@"SIMPLEDEMORPC_IOS", [mPaasAppInterfaceImp class]);
        return UIApplicationMain(argc, argv, @"DFApplication", @"DFClientDelegate");
    }
}
```
添加一段代码模拟 Crash
```
- (void)raiseOutOfRangeException
{
    NSArray * array = @[@"Hello", @"World"];
    NSString * str = [array objectAtIndex:2];
    NSLog(@"String value:%@", str);
}
```
这就是我们的界面，比较简陋。<br>
![interface](https://t.alipayobjects.com/images/rmsweb/T19JXhXXdaXXXXXXXX.png)

OK，我们的 Demo 已经完成了，上传代码到 svn。

### 5.3.6 测试我们的 Demo

首先，上传打包使用的证书（为包含p12 与 mobileprovision 的 zip 压缩包）
![build1](https://t.alipayobjects.com/images/rmsweb/T1vdXhXgtaXXXXXXXX.png)

同时上传苹果 Push 使用的证书（为 p12 文件）
![build2](https://t.alipayobjects.com/images/rmsweb/T1bs0hXbBcXXXXXXXX.png)

构建完成后，安装到手机上，使用`alipayAdmin`账号登录（密码为`ali123`）。登录成功，点击发送 RPC 请求，可以看到 RPC 返回提示语“欢迎进入 mpaas!”<br>
![build3](https://t.alipayobjects.com/images/rmsweb/T1JZxhXixfXXXXXXXX.PNG)

在`云开发->Push->推送消息`界面向我们的登录用户`alipayAdmin`推送一条消息
![build4](https://t.alipayobjects.com/images/rmsweb/T1gb0hXgJkXXXXXXXX.png)
成功接收！<br>
![build5](https://t.alipayobjects.com/images/rmsweb/T1lb4hXnhjXXXXXXXX.PNG)

点击“制造一个 Crash”，让我们的 Demo 模拟一次 Crash，然后重新启动 Demo，让 Crash 上传到服务器。过两分钟，我们就可以在主站上看到这次 Crash 啦！<br>
![build6](https://t.alipayobjects.com/images/rmsweb/T1KsdhXoNhXXXXXXXX.png)

[Demo 的完整代码点此下载](https://t.alipayobjects.com/L1/92/1072/1438847775126.zip)