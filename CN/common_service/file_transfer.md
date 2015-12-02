@cnName 5 文件上传下载
@priority 5

# 5 文件上传下载

[TOC]

## 5.1 简介

移动框架提供文件的上传下载功能，实现静态资源的上传和下载，同时在服务器端的配合下做到断点续传、分片上传功能。（目前分片与断点功能暂未开放）

## 5.2 上传

### 5.2.1 启动一个上传任务
 
```C
NSString* fullPathName = @"...";
NSString* uploadUrl = @"...";
APFileTransferCenter* tcenter = [APFileTransferCenter sharedInstance];
NSInteger taskTag = [tcenter addUpTask:uploadUrl file:fullPathName delegate:self];
if(taskTag > 0)
{
    NSLog(@"UPLOAD[%d] start success", (int)taskTag);
}
else
{
    NSLog(@"UPLOAD[%d] start fail", (int)taskTag);
}
```
* 可在任意线程创建任务
* 参数 fullPathName 必须是带有完整路径的文件名
* 参数 uploadUrl 是带有 http 或 https 格式的完整 url 地址
* 参数 delegate 接收上传任务进度，成功或失败的通知（由主线程通知）
* 返回值用来唯一标识一个上传任务的状态，大于 0 为上传成功，否则表示上传失败

### 5.2.2 APFileUpTaskDelegate

```
@protocol APFileUpTaskDelegate <NSObject>
- (void)APFileUpTaskDidSuccessed:(APFileUpTaskInfo*)info;
- (void)APFileUpTaskDidFailed:(APFileUpTaskInfo*)info;
- (void)APFileUpTaskDidProgressUpdated:(APFileUpTaskInfo*)info;
@end
```

## 5.3 下载

### 5.3.1 启动一个下载任务

```C
NSString* fullPathName = @"...";
NSString* downloadUrl = @"...";
APFileTransferCenter* tcenter = [APFileTransferCenter sharedInstance];
NSInteger taskTag = [tcenter addDownTask:downloadUrl file:fullPathName delegate:self];
if(taskTag > 0)
{
    NSLog(@"DOWNLOAD[%d] start success", (int)taskTag);
}
else
{
    NSLog(@"DOWNLOAD[%d] start fail", (int)taskTag);
}
```
* 创建任务可任意线程调用
* 参数 fullPathName 必须是带有完整路径的文件名
* 参数 downloadUrl 是带有 http 或 https 格式的完整 url 地址
* 参数 delegate 接收下载任务进度，成功或失败的通知（由主线程通知）
* 返回值用来唯一标识一个下载任务，大于 0 为下载成功，否则表示下载失败

### 5.3.2 APFileDownTaskDelegate

```
@protocol APFileDownTaskDelegate <NSObject>
- (void)APFileDownTaskDidSuccessed:(APFileDownTaskInfo*)info;
- (void)APFileDownTaskDidFailed:(APFileDownTaskInfo*)info;
- (void)APFileDownTaskDidProgressUpdated:(APFileDownTaskInfo*)info;
@end
```