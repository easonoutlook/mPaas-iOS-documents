@cnName 4 Guide to Advanced Features
@priority 4

## 4 Guide to Advanced Features

[TOC]

## 4.1 User diagnosis log



### 4.1.1 Functions

The user diagnosis log is not uploaded automatically. It logs files by date and by hour separately.  The developer can push remote commands to the target mobile phone on the primary site, and the mobile phone of the target user will upload the log of a specified time period. 

The user diagnosis log is located in the MPLog module, and references the MPLog/APLog.h.  Usually it only needs to reference mPaas.h. 

The working procedure of the user diagnosis log is as follows: 

* User diagnosis log writes data into local files by time period to form independent log files. 
* The developer passes arguments to the configuration log of the primary site and adds tasks for collecting diagnosis logs. 
* After receiving the tasks, the client packages and uploads log files according to the network type and logging periods.  The developer downloads logs from the primary site for viewing. 

### 4.1.2 Log interface
The interfaces here are all available, but uploading by log level is not supported yet. 
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

### 4.1.3 Creating collection task on primary site

Go to the primary site and select the application. In 'Statistic Analysis'-'Diagnosis', select 'Add Collection Log' - iOS platform.  Input the desired user ID and time periods for log collection, and click "Save". 
![g1](https://os.alipayobjects.com/rmsportal/sHjuTDuUenqKvlo.png)

The task you created can be found in 'View Collection Log'. 
![g2](https://os.alipayobjects.com/rmsportal/aOLlxYNvxgemtfe.png)

Click 'View' to view the execution of the task. If the task is executed and the uploading is successful, you can download the log here. 
![g3](https://os.alipayobjects.com/rmsportal/StvFKzEKmiuZsSf.png)


## 4.2 Client report-active

The client report-active logic is embedded in the framework. If the framework is not used, you can enable report-active feature with the method below. 
```C
[APRemoteLogger writeLogWithActionId:KActionID_Event extParams:nil appId:@"" seed:@"reportActive" ucId:@""];
```

The default report-active logic of mPaaS client framework is: 

* When there is no active application process, report once at application startup. 
* When there are active application processes, report once for a switch from the background to the foreground. 

To lower down the report-active frequency, you can rewrite the following method in mPaasAppInterface and return an interval: 
```C
/**
 *  When the application is switched from the background to the foreground, if the interval between the switch and the last report-active activity is shorter than a certain value, the client will not report again. If the value of "0" is passed for the argument, the client will report active every time the application is switched from the background to the foreground.
 *  This will not affect the application startup when there is no active process, in which case each application startup will trigger the report-active activity. 
 *
 *  @return Number of seconds
 */
- (NSInteger)appReportActiveMinIntervalSeconds;
```

## 4.3 Login reporting

The application can selectively upload user login events and pass userId in the report argument: 
```C
[APRemoteLogger writeLogWithActionId:KActionID_Event extParams:@[@"userId"] appId:@"" seed:@"login" ucId:@""]; 
```


## 4.4 Log module and uploading behavior

### 4.4.1 Working procedure of monitor point

* The monitor point writes logs into local files based on the monitor point interfaces called and incoming arguments. The file structure is one log in one line, with markers at the beginning of each log.  The first figure represents the uploading status of the log. 0 indicates that the log is not uploaded, and 1 indicates that the log is uploaded. 
* Within a log file, if there are 100 logs, or the log size reaches 10 KB (the argument may be subject to slight changes), an uploading action will be triggered. 
* With the uploading successful, the log will be marked as uploaded. Otherwise the log will be uploaded again at the next uploading action triggered. 
* After the log is uploaded to the server, the server will categorize the logs into different log files based on the log file header or other arguments. 
* Crash logs are required to be uploaded in time. So the crash log will be uploaded every time when the application is restarted after a crash. 

### 4.4.2 Log module class diagram

![APLogUML](https://t.alipayobjects.com/images/rmsweb/T1dGhgXgtlXXXXXXXX.png)

