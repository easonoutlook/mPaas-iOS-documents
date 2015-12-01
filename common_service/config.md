@cnName 2 配置服务
@priority 2

# 2 配置服务

[TOC]

# 2.1 简介

配置服务使客户端有能力拉取动态配置的参数，修改客户端行为。可用来进行活动运营、入口控制等功能。

配置服务需要应用后台提供相应的 RPC 接口，客户端在启动时会调用该 RPC 拉取开关配置字典。开发者可以在主站上进行开关的配置。（主站管理配置服务功能还在开发中，后期会对外开放）

## 2.2 使用方法

### 2.2.1 获取配置服务实例

```C
id<MPConfigService> configService = [mPaas() serviceWithName:@"ConfigService"];
```
建议 app 封装一个宏或方法，避免每次都强制类型转换。如果业务未勾选配置服务，会取到 nil。

### 2.2.2 获取某个配置

```C
id<MPConfigService> configService = [mPaas() serviceWithName:@"ConfigService"];
NSString* aConfig = [configService stringValueForKey:@"AConfigName"];
```

### 2.2.3 监听配置更新拉取成功的通知

```C
extern NSString* const MPConfigServiceDidFetchedFromServerNotification; // 从服务器拉到配置后，会抛此通知
```