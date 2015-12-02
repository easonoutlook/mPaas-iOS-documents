@cnName 5 File Upload and Download
@priority 5

# 5 File Upload and Download

[TOC]

## 5.1 Introduction

mPaaS offers file upload and download features to achieve uploading and downloading of the static resources. It also enables breakpoint resume and multi-part upload if the server supports such features.  (At present, breakpoint resume and multi-part upload features are not open yet.) 

## 5.2 Upload

### 5.2.1 Starting an upload task
 
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
* Capable of creating tasks in any thread
* The fullPathName argument must be a file name with the full path. 
* The uploadUrl argument is a full URL with HTTP or HTTPS. 
* The delegate argument receives the notifications about the upload progress, success or failure (The notification is sent by the main thread). 
* The returned value is exclusively used to mark the status of an upload task. When the value is greater than 0, it indicates upload success. Otherwise, it indicates upload failure. 

### 5.2.2 APFileUpTaskDelegate

```
@protocol APFileUpTaskDelegate <NSObject>
- (void)APFileUpTaskDidSuccessed:(APFileUpTaskInfo*)info;
- (void)APFileUpTaskDidFailed:(APFileUpTaskInfo*)info;
- (void)APFileUpTaskDidProgressUpdated:(APFileUpTaskInfo*)info;
@end
```

## 5.3 Download

### 5.3.1 Starting a download task

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
* Create tasks capable of calls in any thread
* The fullPathName argument must be a file name with the full path.
* The downloadUrl argument is a full URL with HTTP or HTTPS.
* The delegate argument receives the notifications about the download progress, success or failure (The notification is sent by the main thread).
* The returned value is exclusively used to mark the status of a download task. When the value is greater than 0, it indicates download success. Otherwise, it indicates download failure. 

### 5.3.2 APFileDownTaskDelegate

```
@protocol APFileDownTaskDelegate <NSObject>
- (void)APFileDownTaskDidSuccessed:(APFileDownTaskInfo*)info;
- (void)APFileDownTaskDidFailed:(APFileDownTaskInfo*)info;
- (void)APFileDownTaskDidProgressUpdated:(APFileDownTaskInfo*)info;
@end
```