@cnName 2 配置 Xcode 工程
@priority 2

# 2 配置 Xcode 工程
[TOC]

你可以选择手工配置一个基于移动框架的工程，通常并不需要这么做。推荐使用开发者工具生成模板工程和管理移动模块。

___请使用开发者工具管理模块。下面流程会帮助你更好的了解基于移动的 Xcode 工程。___

## 2.1 将 mPaas.framework 与资源加入工程

拿到 mPaas.framework 与资源 bundles 后，都加入 Xcode 工程。你只需要引用 mPaas.h 这一个头文件即可。建议创建一个公共的 PCH 文件，这样工程中所有文件都不再需要单独引用头文件了。

```
#ifdef __OBJC__
#import <UIKit/UIKit.h>
#import <mPaas/mPaas.h>
#endif
```

一个工程的目录组织结构如下：

![Screen Shot 2015-01-15 at 12.34.04 PM](https://t.alipayobjects.com/images/rmsweb/T1e.NfXd8cXXXXXXXX.png)
图中 1 为应用需要实现的[接口](env.md)，2 为 mPaas.framework 下的 bundle。

## 2.2 添加系统库

添加 mPaas.framework 后，工程可能编译不通过，需要酌情添加以下系统库：

![Screen Shot 2015-01-19 at 5.43.45 PM](https://t.alipayobjects.com/images/rmsweb/T1x_XfXcRyXXXXXXXX.png)

可能需要添加的头文件搜索路径：

${SDK_ROOT}/usr/include/libxml2

![Screen Shot 2015-01-19 at 5.43.59 PM](https://t.alipayobjects.com/images/rmsweb/T1gEBfXd4eXXXXXXXX.png)

## 2.3 添加第三方依赖

根据 mPaas.framework 包含的组件不同，可能需要依赖一些第三方 framework，也需要添加到工程中。

![Screen Shot 2015-02-10 at 5.25.57 PM](https://t.alipayobjects.com/images/rmsweb/T1YqVgXcxeXXXXXXXX.png)

向 app 的 link 选项中添加-ObjC

![Screen Shot 2015-02-10 at 5.24.35 PM](https://t.alipayobjects.com/images/rmsweb/T1zDRfXo4rXXXXXXXX.png)

## 2.4 接入移动应用框架

请把应用初始化方法修改为下面这样，并在应用的 plist 里把 main storyboard 删除掉。如果不使用移动应用框架，仅使用移动公共组件服务，可以不修改。
```C
UIApplicationMain(argc, argv, @"DFApplication", @"DFClientDelegate");
```
App 可以自定义类继承 DFClientDelegate，在 UIApplicationMain 函数中最后一个参数使用自己的类，但不建议这么做。mPaasAppInterface 类的 appDelegateEvent 已经足够。请参考[环境变量](env.md)
```C
- (id)appDelegateEvent:(mPaasAppEventType)event arguments:(NSDictionary*)arguments;
```

## 2.5 调用 mPaasInit 方法初始化

这个方法是初始化方法，建议在 main 函数 UIApplicationMain 方法前面调用。如果不使用移动应用框架，也可以放在 AppDelegate 的 application:didFinishLaunching 方法里。

```C
/**
*  <#Description#>
*
*  @param appKey            应用的 appKey
*  @param appInterfaceClass 应用环境变量代理接口类
*
*  @return 是否成功
*/
BOOL mPaasInit(NSString* appKey, Class appInterfaceClass);
```