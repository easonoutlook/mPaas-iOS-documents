@cnName 3 快速开始
@priority 3

# 3 快速开始
[TOC]

## 3.1 客户端框架模块

客户端框架模块为 MPMobileFramwork.framework，使用开发者创建的模板应用默认会依赖客户端框架。客户端框架同时依赖 Crash 日志、埋点日志等模块。

## 3.2 使用客户端框架

接入移动客户端框架的核心是将应用的生命周期交给框架来管理，只需要在 main.m 文件中指定使用 DFApplication 与 DFClientDelegate 即可。
```
int main(int argc, char * argv[]) {
    enable_crash_reporter_service();
    @autoreleasepool {
        mPaasInit(@"BASKETBALL_IOS", [mPaasAppInterfaceImp class]);
        return UIApplicationMain(argc, argv, @"DFApplication", @"DFClientDelegate");
    }
}
```

## 3.3 框架上下文（ DTContext）

* 框架的控制中心，各个微应用都通过 context 与框架打交道；
* 提供接口以启动微应用，根据名字查找微应用，关闭微应用，管理微应用跳转等；
* 服务的注册、发现和反注册；

### 3.3.1 获取上下文接口

```C
DTContext * DTContextGet();
```

### 3.3.2 微应用管理相关接口
```C
/**
 * 根据指定的名称启动一个应用。
 *
 * @param name 要启动的应用名。
 * @param params 启动应用时，需要传递给另一个应用的参数。
 * @param animated 指定启动应用时，是否显示动画。
 *
 * @return 应用启动成功返回 YES，否则返回 NO。
 */
- (BOOL)startApplication:(NSString *)name params:(NSDictionary *)params animated:(BOOL)animated;

/**
 * 根据指定的名称启动一个应用。
 *
 * @param name 要启动的应用名。
 * @param params 启动应用时，需要传递给另一个应用的参数。
 * @param launchMode 指定 app 启动的方式。
 *
 * @return 应用启动成功返回 YES，否则返回 NO。
 */
- (BOOL)startApplication:(NSString *)name params:(NSDictionary *)params launchMode:(DTMicroApplicationLaunchMode)launchMode;

/**
 * 查找指定的应用。
 *
 * @param name 要查找的应用名。
 *
 * @return 如果指定的应用已在应用栈中，则返回对应的应用对象；否则返回 nil。
 */
- (DTMicroApplication *)findApplicationByName:(NSString *)name;

/**
 * 返回当前在栈顶的应用，即对用户可见的应用。
 * 
 * @return 当前可见的应用。
 */
- (DTMicroApplication *)currentApplication;
```

### 3.3.3 服务管理相关接口
```C
/**
 * 根据指定的名称查找服务。
 *
 * @param name 服务名
 *
 * @return 如果找到指定名称的服务，则返回一个服务对象，否则返回空。
 */
- (id)findServiceByName:(NSString *)name;

/**
 * 注册一个服务。
 *
 * @param name 服务名
 */
- (BOOL)registerService:(id)service forName:(NSString *)name;

/**
 * 反注册一个已存在的服务。
 *
 * @param name 服务名。
 */
- (void)unregisterServiceForName:(NSString *)name;
```

## 3.4 微应用（ DTMicroApplication）

### 3.4.1 微应用的生命周期
![applicationSchedule](https://os.alipayobjects.com/rmsportal/MHBlKocgpyxzjDo.png)

### 3.4.2 DTMicroApplication
```C
/**
 * 应用的描述信息。
 */
@property(nonatomic, strong) DTMicroApplicationDescriptor *descriptor;

/**
 * 当前 app 的代理。
 */
@property(nonatomic, strong) id <DTMicroApplicationDelegate> delegate;

/**
 * 这个 app 的运行模式。
 */
@property(nonatomic, assign) DTMicroApplicationLaunchMode launchMode;

/**
 * 获取当前应用的根控制器。
 *
 * @return 当前应用的根控制器对象，这个控制器<b>必须</b>是<code>DTViewController</code>的一个子类。
 */
- (UIViewController *)rootController;

/**
 * 退出本应用。
 *
 * @param animated 指定退出应用时，是否需要动画。
 */
- (void)exitAnimated:(BOOL)animated;

/**
 * 处理 push 消息。
 *
 * @param params push 数据。
 *
 * @return 处理成功返回 YES，否则 NO。
 */
- (BOOL)handleRemoteNotifications:(NSDictionary *)params;
```

### 3.4.3 DTMicroApplicationDelegate

```C
@required
/**
 * 请求应用对象的代理返回根视图控制器。
 *
 * @param application 应用对象。
 *
 * @return 应用的根视图控制器。
 */
- (UIViewController *)rootControllerInApplication:(DTMicroApplication *)application;

@optional

/**
 * 通知应用代理，应用对象已经对经被实例化。
 *
 * @param application 应用对象。
 */
- (void)applicationDidCreate:(DTMicroApplication *)application;

/**
 * 通知应用代理，应用将要启动。
 *
 * @param application 启动的应用对象。
 * @param options 应用运行参数。
 */
- (void)application:(DTMicroApplication *)application willStartLaunchingWithOptions:(NSDictionary *)options;

/**
 * 通知应用代理，应用已启动。
 *
 * @param application 启动的应用对象。
 */
- (void)applicationDidFinishLaunching:(DTMicroApplication *)application;

/**
 * 通知应用代理，应用即将暂停进入后台运行。
 *
 * @param application 启动的应用对象。
 */
- (void)applicationWillPause:(DTMicroApplication *)application;

/**
 * 通知应用代理，应用将被重新激活。
 *
 * @param application 要激活的应用对象。
 */
- (void)application:(DTMicroApplication *)application willResumeWithOptions:(NSDictionary *)options;

/**
 * 通知应用代理，应用已经被激活。
 *
 * @param application 要激活的应用对象。
 */
- (void)applicationDidResume:(DTMicroApplication *)application;

/**
 * 通知应用代理，应用已经被激活。
 *
 * @param application 要激活的应用对象，带上参数的版本。
 */
- (void)application:(DTMicroApplication *)application didResumeWithOptions:(NSDictionary *)options;

/**
 * 通知应用的代理，应用将要退出。
 *
 * @param application 应用对象。
 */
- (void)applicationWillTerminate:(DTMicroApplication *)application;

/**
 * 通知应用的代理，应用将要退出。
 *
 * @param application 应用对象。
 * @param animated 是否以动画方式退出。
 */
- (void)applicationWillTerminate:(DTMicroApplication *)application animated:(BOOL)animated;

/**
 * 询问应用的代理，应用是否可以退出。
 * 注意：只用特殊情况返回： NO，要保证默认是 YES 可以退出的。
 *
 * @param application 应用对象。
 *
 * @return 是否可以退出
 */
- (BOOL)applicationShouldTerminate:(DTMicroApplication *)application;
```

## 3.5 服务（ DTService）

* 服务与微应用不同，服务是在后台运行的；
* 服务启动后，通常在整个客户端的生命周期中一直存在，任何时候都可以被获取；
* 服务之间，或服务与微应用之间可以互相调用方法（如执行某个功能或获取数据等）；

### 3.5.1 DTService
```C 
@required

/**
 * 启动一个服务。
 * 注意：
 * 框架在完成初始化操作后，会调用该方法。
 * 如果一个服务要启动一个应用，必须在该方法被调用之后，才能启动其它的应用。
 */
- (void)start;

@optional

/**
 * 创建服务完成。
 */
- (void)didCreate;

/**
 * 服务将要销毁。
 */
- (void)willDestroy;
```

### 3.5.2 创建服务的模板
定义服务的协议（ Protocol）

```C
@protocol MyService <DTService>

// 定义接口
- (void)doTask;

@end
```
实现服务

```C
@interface MyServiceImpl : MyService
@end

@implementation MyServiceImpl

- (void)doTask
{
    // TODO: Add your code here...
}

@end
```

## 3.6 修改微应用与服务配置

使用客户端框架的应用需要添加 MobileRuntime.plist 配置文件（使用模板创建的工程已经有这个文件），结构如下：
![serviceConfig](https://t.alipayobjects.com/images/rmsweb/T1MTJfXaduXXXXXXXX.png)

### 3.6.1 服务配置项

|字段    |说明   |
|-------------|--------|
| name |服务的唯一标识|
| class |服务的实现类，框架在创建该服务时，会利用运行时的反射机制，创建服务实现类的实例|
| lazyLoading |是否延迟加载。如果是延迟加载，在框架启动时，该服务不会被实例化，只有用到该服务时才会实例化并启动。如果是非延迟加载，在框架启动时会实例化并启动该服务。默认为 NO |

### 3.6.2 微应用配置项

|字段    |说明   |
|-------------|--------|
| delegate |应用实现 DTMicroApplicationDelegate 的类名|
| description |应用的描述|
| name |应用的名字，通常使用数字序号，startApplication 时使用 name 来启动应用|
