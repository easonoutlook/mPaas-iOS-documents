@cnName Code Scan Component
@priority 10

# Code Scan Component

[TOC]

## 1 Introduction

MPScanCode module integrates the functions of scanning the bar code, QR code, credit card, etc. It offers quick and low-CPU-consuming code scan service. All the functions are covered in one interface, and the application can select the code scan methods they want to enable. 

## 2 How to use

We recommend mPaaS client framework for creating a micro application for the code scan function. The framework interfaces are also recommended for starting the code scan services. 

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

Configure MobileRuntime.plist. 
![g1](https://t.alipayobjects.com/images/rmsweb/T1ITBhXa4aXXXXXXXX.png)

Start the scanning. 
```
[DTContextGet() startApplication:@"20000016" params:nil animated:YES];
```
