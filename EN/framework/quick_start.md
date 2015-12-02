@cnName 3 Quick Start
@priority 3

# 3 Quick Start
[TOC]

## 3.1 Module of client framework

The client framework module is named 'MPMobileFramwork' in mPaaS, as shown in the figure: 
![g1](https://t.alipayobjects.com/images/rmsweb/T1yCthXbFeXXXXXXXX.png)

You only need to reference the mPaas.h file instead of referencing all the header files separately.  If you used our tools to generate the template project, the reference of the mPaas.h file is also done. 

## 3.2 Using client framework

The core objective of accessing the mPaaS client framework is to manage the application life cycle with the framework. To achieve this, you only need to specify to use DFApplication and DFClientDelegate in the main.m file. 
```
int main(int argc, char * argv[]) {
    enable_crash_reporter_service();
    @autoreleasepool {
        mPaasInit(@"BASKETBALL_IOS", [mPaasAppInterfaceImp class]);
        return UIApplicationMain(argc, argv, @"DFApplication", @"DFClientDelegate");
    }
}
```

## 3.3 Framework context (DTContext)

* The control center of the framework. Every micro application interacts with the framework through the context. 
* It provides interfaces for starting micro applications, searching for micro applications by name, disabling micro applications and managing navigation between micro applications. 
* Registering, discovering and unregistering services. 

### 3.3.1 Getting context interface

```C
DTContext * DTContextGet();
```

### 3.3.2 Interface of micro application management
```C
/**
 * Start an application with the specified name.
 *
 * @param name Name of the application to be started. 
 * @param params Parameters to be passed to another application when the application is started. 
 * @param animated Whether to display animations when the specified application is started. 
 *
 * @return YES is returned for successful application start; otherwise, NO. 
 */
- (BOOL)startApplication:(NSString *)name params:(NSDictionary *)params animated:(BOOL)animated;

/**
 * Start an application with the specified name.
 *
 * @param name Name of the application to be started.
 * @param params Parameters to be passed to another application when the application is started.
 * @param launchMode Specifies the startup mode of the application. 
 *
 * @return YES is returned for successful application start; otherwise, NO.
 */
- (BOOL)startApplication:(NSString *)name params:(NSDictionary *)params launchMode:(DTMicroApplicationLaunchMode)launchMode;

/**
 * Look for the specified application. 
 *
 * @param name Name of the application you are looking for.
 *
 * @return If the specified application is already in the application stack, the application object will be returned. Otherwise nil will be returned. 
 */
- (DTMicroApplication *)findApplicationByName:(NSString *)name;

/**
 * Return applications on the top of the stack, i.e., applications visible to the user. 
 * 
 * @return Currently visible applications. 
 */
- (DTMicroApplication *)currentApplication;
```

### 3.3.3 Interfaces of service management
```C
/**
 * Look for a service with the specified name.
 *
 * @param name Service name. 
 *
 * @return If the specified application is found, a service object will be returned. Otherwise nil will be returned. 
 */
- (id)findServiceByName:(NSString *)name;

/**
 * Register a service. 
 *
 * @param name Service name.
 */
- (BOOL)registerService:(id)service forName:(NSString *)name;

/**
 * Unregister an existing service. 
 *
 * @param name Service name.
 */
- (void)unregisterServiceForName:(NSString *)name;
```

## 3.4 Micro application (DTMicroApplication)

### 3.4.1 Life cycle of micro application
![applicationSchedule](https://os.alipayobjects.com/rmsportal/MHBlKocgpyxzjDo.png)

### 3.4.2 DTMicroApplication
```C
/**
 * Descriptions of the application. 
 */
@property(nonatomic, strong) DTMicroApplicationDescriptor *descriptor;

/**
 * Delegate of the current application. 
 */
@property(nonatomic, strong) id <DTMicroApplicationDelegate> delegate;

/**
 * Run mode of the application. 
 */
@property(nonatomic, assign) DTMicroApplicationLaunchMode launchMode;

/**
 * Get the root controller of the current application. 
 *
 * @return The root controller object of the current application. This controller <b>must</b> be a subclass of <code>DTViewController</code>. 
 */
- (UIViewController *)rootController;

/**
 * Close the application. 
 *
 * @param animated Whether to display animations when the specified application is closed. 
 */
- (void)exitAnimated:(BOOL)animated;

/**
 * Process Push messages. 
 *
 * @param params Push data. 
 *
 * @return YES is returned for successful processing. Otherwise, NO. 
 */
- (BOOL)handleRemoteNotifications:(NSDictionary *)params;
```

### 2.3.4.3 DTMicroApplicationDelegate

```C
@required
/**
 * Request the application object delegate to return the root view controller. 
 *
 * @param application Application object. 
 *
 * @return The root view controller of the application. 
 */
- (UIViewController *)rootControllerInApplication:(DTMicroApplication *)application;

@optional

/**
 * Notify the application delegate that the application object has been instantiated. 
 *
 * @param application Application object.
 */
- (void)applicationDidCreate:(DTMicroApplication *)application;

/**
 * Notify the application delegate that the application is about to start. 
 *
 * @param application The started application object.
 * @param options Operational parameters of the application. 
 */
- (void)application:(DTMicroApplication *)application willStartLaunchingWithOptions:(NSDictionary *)options;

/**
 * Notify the application delegate that the application has been started.
 *
 * @param application The started application object.
 */
- (void)applicationDidFinishLaunching:(DTMicroApplication *)application;

/**
 * Notify the application delegate that the application is about to run in the background. 
 *
 * @param application The started application object.
 */
- (void)applicationWillPause:(DTMicroApplication *)application;

/**
 * Notify the application delegate that the application is about to be re-activated. 
 *
 * @param application The application object to be activated.
 */
- (void)application:(DTMicroApplication *)application willResumeWithOptions:(NSDictionary *)options;

/**
 * Notify the application delegate that the application has been activated.
 *
 * @param application The application object to be activated.
 */
- (void)applicationDidResume:(DTMicroApplication *)application;

/**
 * Notify the application delegate that the application has been activated.
 *
 * @param application The application object to be activated, with the argument version. 
 */
- (void)application:(DTMicroApplication *)application didResumeWithOptions:(NSDictionary *)options;

/**
 * Notify the application delegate that the application is about to exit.
 *
 * @param application Application object.
 */
- (void)applicationWillTerminate:(DTMicroApplication *)application;

/**
 * Notify the application delegate that the application is about to exit.
 *
 * @param application Application object.
 * @param animated Whether to exit with animations. 
 */
- (void)applicationWillTerminate:(DTMicroApplication *)application animated:(BOOL)animated;

/**
 * Inquire the application delegate about whether the application is ready to exit. 
 * Attention: Make sure to return NO only for special circumstances, and   by default, YES should be returned, indicating the application is ready for the exit. 
 *
 * @param application Application object.
 *
 * @return Ready for the exit?
 */
- (BOOL)applicationShouldTerminate:(DTMicroApplication *)application;
```

## 3.5 Service (DTService)

* Services are slightly different from micro applications in that services run in the background. 
* After a service is started, it usually exists throughout the life cycle of the client and can be accessed at any time. 
* Methods can be called between services or between services and micro applications (for example, for executing a function or obtaining data). 

### 3.5.1 DTService
```C 
@required

/**
 * Start a service.
 * Attention: 
 * The framework will call this method after it completes the initialization. 
 * If a service wants to start an application, it cannot start other applications until this method is called. 
 */
- (void)start;

@optional

/**
 * The service is created. 
 */
- (void)didCreate;

/**
 * The service will be destroyed. 
 */
- (void)willDestroy;
```

### 3.5.2 Creating service template
Define the service protocol.

```C
@protocol MyService <DTService>

// Define the interface.
- (void)doTask;

@end
```
Implement the service. 

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

## 3.6 Modifying micro application and service configuration

To use the client framework, the application should incorporate the MobileRuntime.plist configuration file (projects created from the template already have this file). The structure is as follows: 
![serviceConfig](https://t.alipayobjects.com/images/rmsweb/T1MTJfXaduXXXXXXXX.png)

### 3.6.1 Service configuration item

|Field    |Description   |
|-------------|--------|
| name |Unique ID of the service|
| class |The implementation class of services. When creating the service, the framework will utilize runtime reflection to create instances of the service implementation class|
| lazyLoading |Whether to enable lazy loading.  If lazy loading is enabled, the service will not be instantiated at the framework startup. Instead, the service will be instantiated and started only when it is used.  If lazy loading is not enabled, the service will be instantiated and started at the framework startup.  By default, the value is NO. |

### 3.6.2 Micro application configuration item

|Field    |Description   |
|-------------|--------|
| delegate |The class name of DTMicroApplicationDelegate implemented by the application|
| description |Descriptions of the application|
| name |Application name, usually in digit serial number. The startApplication method uses the name to start the application|

