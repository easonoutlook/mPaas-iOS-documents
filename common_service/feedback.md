@cnName 4 用户反馈
@priority 4

# 4 用户反馈

[TOC]

## 4.1 简介

移动框架里集成了一个简单实用的用户反馈界面，并集成了上传图片的功能。同时公开了用户反馈的接口，接入方可以重写自己的 UI 界面。

![Screen Shot 2015-05-26 at 10.35.15 AM](https://t.alipayobjects.com/images/rmsweb/T1X_4fXodnXXXXXXXX.png)

## 4.2 使用方法

用户反馈组件为 MPFeedbackKit。当使用移动框架提供的反馈界面时，可以在接入应用的 MobileRuntime.plist 文件中将用户反馈配置为一个微应用，如图：
![Screen Shot 2015-05-26 at 10.37.02 AM](https://t.alipayobjects.com/images/rmsweb/T1h.RfXmVaXXXXXXXX.png)

应用的名字可以由接入方自己定，只要是唯一的子应用 ID 即可。然后在代码中使用下面代码就可以唤起用户反馈界面：
```
[DTContextGet() startApplication:@"20000018" params:@{@"userId":@"111111", @"mobileNo":@"13700033333"} animated:YES];
```
其中`userId`和`mobileNo`是可选参数，为第三方应用当前登录用户的信息，方便收到反馈后联系用户，可以不设置。