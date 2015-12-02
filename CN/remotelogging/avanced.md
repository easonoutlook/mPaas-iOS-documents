@cnName 4 进阶指南
@priority 4

# 4 进阶指南

[TOC]

## 4.1 用户诊断日志

### 4.1.1 功能介绍

用户诊断日志是非自动上传的日志，会按照日期与小时分别记录文件。开发者可以在主站通过远程命令向目标手机发送推送，目标用户的手机会将特定时间段的日志文件上传。

诊断日志所在模块为 MPLog，引用的头文件为 MPLog/APLog.h。通常只需要引用 mPaas.h 即可。

诊断日志工作流程如下：

* 按照时间段写入本地文件，形成独立的日志文件。
* 开发者在主站配置日志上传参数，添加采集诊断日志的任务。
* 客户端收到任务后，根据网络类型和日志时间段，打包日志文件上传。开发者在主站下载日志查看。

### 4.1.2 日志接口
这几个接口都可以，目前还不支持分级别上传的功能。
```
#define APLogError(tag,fmt, ...) \
APLogToFile(tag, kAPLogLevelError, fmt, ##__VA_ARGS__)

#define APLogWarn(tag,fmt, ...) \
APLogToFile(tag, kAPLogLevelWarn, fmt, ##__VA_ARGS__)

#define APLogInfo(tag,fmt, ...) \
APLogToFile(tag, kAPLogLevelInfo, fmt, ##__VA_ARGS__)

#define APLogDebug(tag,fmt, ...) \
APLogToFile(tag, kAPLogLevelDebug, fmt, ##__VA_ARGS__)
```

### 4.1.3 在主站创建采集任务

进入主站，选择应用，在`统计分析`-`诊断`中选择`添加采集日志`，并选择 iOS 平台。输入需要采集的用户 ID 与时间段，点击保存。
![g1](https://t.alipayobjects.com/images/rmsweb/T1dndhXhtXXXXXXXXX.png)

可以在`查看采集日志`中看到自己创建的任务。
![g2](https://t.alipayobjects.com/images/rmsweb/T1ERRhXg0jXXXXXXXX.png)

点击`查看`，可以看到任务执行情况，如果执行并上传成功，可以在这里下载日志。
![g3](https://t.alipayobjects.com/images/rmsweb/T1tBVhXbVjXXXXXXXX.png)

采集日志需要客户端支持静默Push，如图
![image](https://os.alipayobjects.com/rmsportal/xiWZxvemFUIqfWr.png)

## 4.2 客户端报活

客户端报活逻辑已经预埋在框架中，如果不使用框架，可以使用下面的方法报活。
```C
[APRemoteLogger writeLogWithActionId:KActionID_Event extParams:nil appId:@"" seed:@"reportActive" ucId:@""];
```

移动客户端框架默认的报活逻辑为：

* 无应用进程时，启动时上报一次；
* 有应用进程时，从后台切回前台，上报一次。

如果想降低报活频率，可以重写 mPaasAppInterface 里下面的方法，并返回一个时间间隔：
```C
/**
 *  从后台切回前台时，距离上次报活时间少于多少时，不再报活。如果传 0，每次后台切回前台都会报活。
 *  这个不影响无进程启动，如果无进程启动，每次都会报活。
 *
 *  @return 返回秒数
 */
- (NSInteger)appReportActiveMinIntervalSeconds;
```

## 4.3 登录上报

应用可以选择性的上传用户登录事件，并在上报参数里上传 userId：
```C
[APRemoteLogger writeLogWithActionId:KActionID_Event extParams:@[@"userId"] appId:@"" seed:@"login" ucId:@""]; 
```


## 4.4 日志模块与上传行为

[TOC]

### 4.4.1 埋点工作流程

* 根据所调用的埋点接口和所传入的参数将日志写入本地文件，文件的组织格式是一行表示一条 log，每条 log 开头有标记信息。其中第一个数字代表日志的上传状态，0 表示日志还没有上传，1 代表日志已经上传。
* 在一个日志文件的范围内，如果满足 100 条或者写入的日志已经够 10KB（参数可能有微调），则触发一次上传操作。
* 上传成功则标记日志已经上传过，否则等待下次触发继续上传。
* 日志上传到服务器后，服务器根据日志的文件头或者其他参数将日志归入不同的日志文件。
* Crash 日志由于需要及时保证上传，每次发生 Crash 的时候会在下次启动时触发上传。

### 4.4.2 日志模块类图

![APLogUML](https://t.alipayobjects.com/images/rmsweb/T1dGhgXgtlXXXXXXXX.png)