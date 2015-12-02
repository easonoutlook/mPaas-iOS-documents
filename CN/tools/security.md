@cnName iOS 安全检测组件
@priority 5

# iOS 安全检测组件

[TOC]

## 1 简介

APSecurityUtility 提供一系列实用的安全检测接口，主要包括运行时 Hook 检查、外部调用检查、调试状态检查等。

调用 Hook 检测接口前，应先明确使用场景。根据编程语言、是否检测 iOS 系统 Framework API 函数、或自定义函数等条件，正确选择对应的检测接口。

## 2 接口介绍

### 2.1 APDetectClassSelectorSwizzling
```
/**
 *  在调用一个(+)selector 前，检查该 selector 是否被外部注入的库 hook
 *  仅适用于本应用程序内(包含静态库)实现的 selector，不能使用此方法检测 iOS 系统 Framework API 及其他 dylib 中定义的 API
 * 
 *  (BOOL)APDetectClassSelectorSwizzling(NSString *className, SEL sel)
 *
 *  ------------------------------------------------------
 *  @param className selector 所属类名
 *  @param sel       selector
 *
 *  @return 返回值类型 BOOL，返回 YES 时表示该 selector 已被 hook
 *  ------------------------------------------------------
 *
 *  示例如下:
 *  BOOL isHooked = APDetectClassSelectorSwizzling(@"SomeClass", @selector(test));
 *
 */
```

### 2.2 APDetectInstanceSelectorSwizzling
```
/**
 *  在调用一个(-)selector 前，检查该 selector 是否被外部注入的库 hook
 *  仅适用于本应用程序内(包含静态库)实现的 selector，不能使用此方法检测 iOS 系统 Framework API 其他 dylib 中定义的 API
 *
 *  (BOOL)APDetectInstanceSelectorSwizzling(NSString *className, SEL sel)
 *
 *  ------------------------------------------------------
 *  @param className selector 所属类名
 *  @param sel       selector
 *
 *  @return 返回值类型 BOOL，返回 YES 时表示该 selector 已被 hook
 *  ------------------------------------------------------
 *
 *  示例如下:
 *  BOOL isHooked = APDetectInstanceSelectorSwizzling(@"SomeClass", @selector(test));
 *
 */
```

### 2.3 APDetectCFuncCalledByOthers & APDetectNonFrameworkSelectorCalledByOthers
```
/**
 *  在 C 函数内部实现中加入此检测方法，检查该函数是否被外部注入的程序调用，如 gdb、外部注入的 dylib 等
 *
 *  示例如下：
 *  bool isRisky = APDetectCFuncCalledByOthers;
 *
 */
```

### 2.4 APDetectUIKitInstanceSelectorSwizzling
```
/**
 *  在调用一个 UIKit 里(-)selector 前，检查该 selector 是否被外部注入的库 hook，即系统 API 是否被非己 hook
 *  仅适用于 iOS 系统 UIKit Framework 中定义的 API
 *
 *  (BOOL)APDetectUIKitInstanceSelectorSwizzling(NSString *className, SEL sel)
 *
 *  ------------------------------------------------------
 *  @param className selector 所属类名
 *  @param sel       selector
 *
 *  @return 返回值类型 BOOL，返回 YES 时表示该 selector 已被 hook
 *  ------------------------------------------------------
 *
 *  示例如下:
 *  BOOL isHooked = APDetectUIKitInstanceSelectorSwizzling(@"UITextfield", @selector(text));
 *
 */
```

### 2.5 APDetectLocalAuthInstanceSelectorSwizzling
```
/**
 *  在调用一个 LocalAuthentication.framework 里(-)selector 前，检查该 selector 是否被外部注入的库 hook，即系统 API 是否被非己 hook
 *  仅适用于 iOS 系统 LocalAuthentication.framework 中定义的 API
 *  不适用于模拟器
 *
 *  注意：调用方需自行保证 link LocalAuthentication.framework (可设置 optional)
 *       当运行在 iOS8 以下的系统 link 不到 LocalAuthentication.framework 时，默认返回 NO
 *
 *  (BOOL)APDetectLocalAuthInstanceSelectorSwizzling(NSString *className, SEL sel)
 *
 *  ------------------------------------------------------
 *  @param className selector 所属类名
 *  @param sel       selector
 *
 *  @return 返回值类型 BOOL，返回 YES 时表示该 selector 已被 hook
 *  ------------------------------------------------------
 *
 *  示例如下:
 *  BOOL isHooked = APDetectLocalAuthInstanceSelectorSwizzling(@"LAContext", @selector(canEvaluatePolicy:error:));
 *
 */
```

### 2.6 APDetectJailbroken
```
/**
 *  越狱检测
 *  
 *  (BOOL)APDetectJailbroken;
 */
```

### 2.7 APDetectDebugger
```
/**
 *  检查应用当前是否处于调试状态
 *  (BOOL)APDetectDebugger;
 *
 *  @return 返回值类型 BOOL，返回 YES 时表示当前为调试状态
 */
```

### 2.8 APGetTextSectionDigest
```
/**
 *  获取可执行程序二进制的代码段摘要
 *
 *  (NSString *)APGetTextSectionDigest;
 */
```

### 2.9 APDetectLinkerFlag
```
/**
 *  检查二进制可执行程序的 linker flag 的 restrict 标志
 *
 *  (BOOL)APDetectLinkerFlag;
 *  @return 返回值类型 BOOL，返回 YES 时表示当前为存在
 */
```

### 2.10 APCheckNotificationSecurity
```
/**
 *  接收到通知后，检查通知发送者是否出自应用内部。当第三方插件发送通知名为 name 的通知时，返回 NO，表示存在风险.
 *  特别注意：需保证通知名字符串为静态字符串，若通知发送者以 format 拼接通知名，此方法返回 NO，不符合预期。
 *
 *  Sample:
 *
 *  NSString *name = [notification name];
 *  if (!APCheckNotificationSecurity(name)) {
 *      //不响应钱包外部发送的通知
 *      return;
 *  }
 *
 *  @return 返回值类型 BOOL，返回 YES 时表示安全
 */
```