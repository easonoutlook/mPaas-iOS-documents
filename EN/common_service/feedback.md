@cnName 4 User Feedback
@priority 4

# 4 User Feedback

[TOC]

## 4.1 Introduction

mPaaS integrates a simple but useful interface for user feedbacks with the image uploading feature.  It also opens the interface for user feedbacks and access parties can rewrite their own UI interfaces. 

![Screen Shot 2015-05-26 at 10.35.15 AM](https://t.alipayobjects.com/images/rmsweb/T1X_4fXodnXXXXXXXX.png)

## 4.2 How to use

The user feedback component is MPFeedbackKit.  When the feedback interface provided by mPaaS is used, the developer than configure the user feedback as a micro application in the MobileRuntime.plist file of the accessed application, as shown in the figure: 
![Screen Shot 2015-05-26 at 10.37.02 AM](https://t.alipayobjects.com/images/rmsweb/T1h.RfXmVaXXXXXXXX.png)

The micro application name can be customized by the access party, as long as it is a unique sub-application ID.  The user feedback interface can be evoked with the following codes: 
```
[DTContextGet() startApplication:@"20000018" params:@{@"userId":@"111111", @"mobileNo":@"13700033333"} animated:YES];
```
The 'userId' and 'mobileNo' arguments are optional. They are the information of the user currently logged in on the third-party application, for the convenience of contacting the user after the user's feedback is received. 