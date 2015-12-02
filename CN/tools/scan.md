@cnName 扫码组件
@priority 10

# 扫码组件

[TOC]

## 1 简介

MPScanCode 模块整合了条码、二维码、信用卡等扫描功能，提供快速、低 CPU 开销的扫码服务，所有功能整合进入一个界面，应用可以选择开启的扫码方式。

## 2 使用方法

推荐使用移动客户端框架为扫码功能创建一个微应用，并通过框架接口启动扫码服务。

```
@interface ScanCodeDelegate () <BumpControllerDelegate>

@property(nonatomic, strong) UIViewController *rootController;

@end

@implementation ScanCodeDelegate

- (UIViewController *)rootControllerInApplication:(DTMicroApplication *)application
{
    return self.rootController;
}

- (void)application:(DTMicroApplication *)application willStartLaunchingWithOptions:(NSDictionary *)launchOptions
{
    self.rootController = [[ScanCodeViewController alloc] initWithNibName:nil bundle:nil withType:@[@(QRCODE_TAB), @(CREDIT_TAB)]];
    ((ScanCodeViewController*)self.rootController).bumpDelegate = self;
}

@end
```

配置 MobileRuntime.plist
![g1](https://t.alipayobjects.com/images/rmsweb/T1ITBhXa4aXXXXXXXX.png)

启动扫码
```
[DTContextGet() startApplication:@"20000016" params:nil animated:YES];
```