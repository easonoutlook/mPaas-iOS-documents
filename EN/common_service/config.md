@cnName 2 Configuration Service
@priority 2

# 2 Configuration Service

[TOC]

# 2.1 Introduction

The Configuration Service enables the client to pull arguments of dynamic configurations and modify client behaviors.  It can be utilized for activity operation and access control. 

The Configuration Service requires the corresponding RPC interface from the application background. When the client is started, it will invoke the RPC interface to acquire the switch configuration dictionary.  The developer can configure the switch on the primary site.  (The configuration service management on the primary site is still under development. It will be open to the public in the future.)

## 2.2 How to use

### 2.2.1 Obtaining Configuration Service instance

```C
id<MPConfigService> configService = [mPaas() serviceWithName:@"ConfigService"];
```
It is recommended that the application encapsulate a macro or method to avoid mandatory type conversion every time.  If the Configuration Service is not checked when the business accesses mPaas, nil will be returned. 

### 2.2.2 Obtaining configuration

```C
id<MPConfigService> configService = [mPaas() serviceWithName:@"ConfigService"];
NSString* aConfig = [configService stringValueForKey:@"AConfigName"];
```

### 2.2.3 Notification on successful pull of configuration updates

```C
extern NSString* const MPConfigServiceDidFetchedFromServerNotification; // This notification will be thrown when the configuration is pulled successfully from the server. 
```
