@cnName 4 使用系统设置服务
@priority 4

# 4 使用系统设置服务
[TOC]
## 4.1 概述

开发者可以在系统设置里为自己的应用添加设置功能。这个功能通常可以用来在开发阶段配置服务器地址，实现在不换包的情况下切换环境。

使用开发者工具生成模板工程后，工程里会附带 Settings.bundle。
![g1](https://t.alipayobjects.com/images/rmsweb/T1c9dhXftgXXXXXXXX.png)

对应在系统设置里的选项如下：

![g2](https://t.alipayobjects.com/images/rmsweb/T14m8hXfVXXXXXXXXX.png)

`Category`里有`Customizing`, `Release`, `Pre-release`, `Debug`几个选项。选择`Customizing`后，可以进入`Customizing`里设置具体的环境地址。重启应用即可生效。

## 4.2 配置设置服务

### 4.2.1 打开设置服务开关

mPaasAppInterface 的实现类里，appUseSettingService 方法返回 YES。
```C
- (BOOL)appUseSettingService
{
    return YES;
}
```
这样会使用工程中的 GatewayConfig.plist 配置的环境变量。
![g3](https://t.alipayobjects.com/images/rmsweb/T1R9phXehfXXXXXXXX.png)

开发者只需要在 GatewayConfig.plist 相应环境下配置需要的地址即可。字典里的 Key 值不可修改。

### 4.2.2 预置的设置项

|名字|格式样例|含义|
|-|-|-|
| mPaasPushServerGateway |无|暂时无用，可忽略或删除|
| mPaasPushAppId | THFUND | Push 服务应用 ID，与 Android 相同，不带平台信息。与 Appkey 不同。|
| mPaasLogServerGateway | http://mdap.alipay.com |日志服务器地址，这个地址是埋点，Crash 上报，用户诊断系统的服务器地址。|
| mPaasLogProductId | THFUND_IOS-0000017768 |日志服务应用 ID，通常为带平台的 Appkey 与 WorkspaceId 拼成。|
| mPaasRpcGateway | http://mobilegw.d1366.alipay.net/mgw.htm | RPC 服务网关地址，线上应该为 https，开发调试可以使用 http。|
| mPaasRpcETagURL |同上|同上|

### 4.2.3 不使用设置服务

当不使用设置服务时，也就是 appUseSettingService 方法返回 NO。会使用 mPaasAppInterface 的回调方法向接入应用请求环境地址。具体涉及的方法如下：
```C
/**
 *  RPC 的服务器地址
 */
- (NSString*)appRPCGatewayURL;

/**
 *  RPC 的 ETag 服务器地址
 */
- (NSString*)appRPCETagURL;

/**
 *  Push 的 AppId
 */
- (NSString*)appPushAppId;

/**
 *  远程日志服务器的地址
 */
- (NSString*)appRemoteLogServerURL;

/**
 *  日志服务的应用名，可能需要加一些参数，与 product id 可能不同
 */
- (NSString*)appRemoteLogProductId;
```