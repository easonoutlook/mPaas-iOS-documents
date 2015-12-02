@cnName 3 Quick Start
@priority 3

# 3 Quick Start

[TOC]

## 3.1 Monitor module

A complete monitor module consists of the following components: 

|Library|Function|
|-|-|
| MPRemoteLogging |Logging module of monitor points|
| MPCrashReporter |Log capturing module|
| MPMonitor |Automatic monitor point and automatic performance monitoring module |
| MPLog |User-action log module|

You only need to reference the mPaas.h file instead of referencing all the header files of these libraries separately. If you used our tools to generate the template project, the reference of the mPaas.h file is also done.

## 3.2 Configuring monitoring gateway URL and application ID

When the system Setting Service is enabled, configure the monitoring gateway URL and application ID to GatewayConfig.plist. 
![g1](https://t.alipayobjects.com/images/rmsweb/T1kR8hXm4hXXXXXXXX.png)

If the system Setting Service is not enabled, return the log configuration information with the two methods below in the implementation class of mPaasAppInterface. 
```
/**
 *  URL of the remote log server
 */
- (NSString*)appRemoteLogServerURL;

/**
 *  Name of the log service application. Some arguments may be required. It may be different from the product ID in mPaaS.
 */
- (NSString*)appRemoteLogProductId;
```

For more information, see [Using Setting Service](../appendix/settings.md)
