@cnName 2 Configuring Xcode Project
@priority 2

# 2 Configuring Xcode Project
[TOC]

You can configure a project based on mPaas.framework manually,  but it is usually not necessary, as you can generate a template project using developer tools.

___This chapter can be skipped, but we recommend you read it through for better understanding in mPaaS-based Xcode projects.  ___

## 2.1 Adding mPaas.framework and resources to a project

Add the ready mPaas.framework and resource bundles to the Xcode project.  You only need to reference the mPaas.h file.  It is recommended that you create a public PCH file so that you don't need to reference the header file separately for all the files in the project. 

```
#ifdef __OBJC__
#import <UIKit/UIKit.h>
#import <mPaas/mPaas.h>
#endif
```

The directory structure of a project is as follows: 

![Screen Shot 2015-01-15 at 12.34.04 PM](https://t.alipayobjects.com/images/rmsweb/T1e.NfXd8cXXXXXXXX.png)
In the figure, Section 1 shows the [Interface](env.md) that the application needs to implement, and Section 2 shows the bundle under the mPaas.framework. 

## 2.2 Adding library

After mPaas.framework is added, the project compiling may fail and the following libraries can be added as appropriate: 

![Screen Shot 2015-01-19 at 5.43.45 PM](https://t.alipayobjects.com/images/rmsweb/T1x_XfXcRyXXXXXXXX.png)

The header file search paths that may be added: 

${SDK_ROOT}/usr/include/libxml2

![Screen Shot 2015-01-19 at 5.43.59 PM](https://t.alipayobjects.com/images/rmsweb/T1gEBfXd4eXXXXXXXX.png)

## 2.3 Adding third-party dependency

Some components contained in mPaas.framework may depend on some third-party frameworks and such third-party frameworks should also be added to the project. 

![Screen Shot 2015-02-10 at 5.25.57 PM](https://t.alipayobjects.com/images/rmsweb/T1YqVgXcxeXXXXXXXX.png)

Add -ObjC to the link option of the application

![Screen Shot 2015-02-10 at 5.24.35 PM](https://t.alipayobjects.com/images/rmsweb/T1zDRfXo4rXXXXXXXX.png)

## 2.4 Accessing mPaaS application framework

Modify the application initialization method as follows and delete the main storyboard in the plist file of the application.  The modification will not be necessary if the mPaaS application framework is not used, and only mPaaS public components and services are used. 
```C
UIApplicationMain(argc, argv, @"DFApplication", @"DFClientDelegate");
```
The application can custom a class inherited from the DFClientDelegate, and use the self-defined class in the last argument of UIApplicationMain function, which is not recommended though.  The appDelegateEvent in mPaasAppInterface class is enough for use.  Please see [Environment Variable](env.md)
```C
- (id)appDelegateEvent:(mPaasAppEventType)event arguments:(NSDictionary*)arguments;
```

## 2.5 Initializing mPaaS by calling mPaasInit method

This is the initialization method of mPaaS and is recommended to be called prior to the UIApplicationMain method of the main function.  If the mPaaS application framework is not applied, the application:didFinishLaunching method of AppDelegate can be used. 

```C
/**
*  <#Description#>
*
*  @param appKey            The AppKey of the application
*  @param appInterfaceClass The interface class of the application environment variable delegate
*
*  @return Successful or not
*/
BOOL mPaasInit(NSString* appKey, Class appInterfaceClass);
```


