@cnName 4 Using Setting Service
@priority 4

# 4 Using Setting Service
[TOC]
## 4.1 Overview

Developers can add the settings function for their applications in system settings.  This function is usually used to configure the server address in the development phrase for the purpose of switching environment without changing the package. 

After a template project is  generated using developer tools, the project will contain the Settings.bundle. 
![g1](https://t.alipayobjects.com/images/rmsweb/T1c9dhXftgXXXXXXXX.png)

The corresponding options in the system Settings are as follows: 
![g2](https://t.alipayobjects.com/images/rmsweb/T14m8hXfVXXXXXXXXX.png)

In 'Category', the 'Customizing', 'Release', 'Pre-release', and 'Debug' options are provided.  The specific environment address can be configured in 'Customizing'.  The configurations will take effect after the application is restarted. 

## 4.2 Configuring Setting Service

### 4.2.1 Enabling Setting Service

In the implementation class of mPaasAppInterface, when the appUseSettingService method returns YES, it indicates that the Setting Service has been enabled.  
```C
- (BOOL)appUseSettingService
{
	return YES;
}
```
The mPaaS will use environment variables configured in the GatewayConfig.plist file in the project. 
![g3](https://t.alipayobjects.com/images/rmsweb/T1R9phXehfXXXXXXXX.png)

The developer only needs to configure the desired URL in the corresponding environment in GatewayConfig.plist.  The key value in the dictionary cannot be modified. 

### 4.2.2 Preset settings

|Name|Format Style|Meaning|
|-|-|-|
| mPaasPushServerGateway |N/A|Not used for the moment. You can ignore or remove it.|
| mPaasPushAppId | THFUND | Application ID of the Push service. It has no platform information, which is the same as that in Android.  It is different from the AppKey of mPaaS.  |
| mPaasLogServerGateway | http://mdap.alipay.com |URL of the log server which is the server address of the monitor point, reporting of crashes and user diagnosis system.  |
| mPaasLogProductId | THFUND_IOS-0000017768 |ID of the log service application. It is usually composed of the AppKey (with platform) and the WorkspaceId.  |
| mPaasRpcGateway | http://mobilegw.d1366.alipay.net/mgw.htm | URL of the gateway of the RPC service. When the application is available online, HTTPS should be used, while for development and debugging, HTTP can be used.   |
| mPaasRpcETagURL |Ditto|Ditto|

### 4.2.3 Not using Setting Service

When the Setting Service is not used, i.e., when the appUseSettingService method returns NO,  mPaaS will request the environment address from the accessed application using callback methods of mPaasAppInterface.  The specific methods involved are as follows: 
```C
/**
 *  RPC server URL
 */
- (NSString*)appRPCGatewayURL;

/**
 *  ETag server URL of the RPC service
 */
- (NSString*)appRPCETagURL;

/**
 *  AppId of the Push service
 */
- (NSString*)appPushAppId;

/**
 *  URL of the remote log server
 */
- (NSString*)appRemoteLogServerURL;

/**
 *  Name of the log service application. Some arguments may be required. It may be different from the product ID in mPaaS.
 */
- (NSString*)appRemoteLogProductId;
```
