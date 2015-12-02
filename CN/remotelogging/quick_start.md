@cnName 3 快速开始
@priority 3

# 3 快速开始

[TOC]

## 3.1 监控模块

完整的监控模块包括以下几个组成部分：

|库|功能|
|-|-|
| MPRemoteLogging |日志埋点处理模块|
| MPCrashReporter |日志捕获模块|
| MPMonitor |自动化埋点与自动化性能监控模块|
| MPLog |行为日志模块|

你不需要单独引用这些库的头文件，只需要引用 mPaas.h 即可。如果使用我们的工具生成模板工程，这也会为你做好。

## 3.2 配置监控的网关地址与应用 ID

当使用系统设置服务时，请将监控的网关地址与应用 ID 配置到 GatewayConfig.plist 文件中。
![g1](https://t.alipayobjects.com/images/rmsweb/T1kR8hXm4hXXXXXXXX.png)

如果不使用系统设置服务，请在 mPaasAppInterface 接口的实现类里，使用下面两个方法将日志配置信息返回。
```
/**
 *  远程日志服务器的地址
 */
- (NSString*)appRemoteLogServerURL;

/**
 *  日志服务的应用名，可能需要加一些参数，与 product id 可能不同
 */
- (NSString*)appRemoteLogProductId;
```

更多信息请参考[使用系统设置服务](../appendix/settings.md)