@cnName 5 FAQs
@priority 5

# 5 FAQs

[TOC]

I. How to use your UIApplication delegate class? 

You can declare that your class inherits from DFClientDelegate, overwrite the DFClientDelegate method, and start the application using your class in main.m. 

II. How to exit all micro applications and back to the Launcher? 

```
[DTContextGet() startApplication:@"AppId of Launcher" params:nil animated:kDTMicroApplicationLaunchModePushNoAnimation]; 
```

III. If Application B exists on top of Application A, how can Application B restart Application A and pass arguments? 

Suppose Application A is started, and Application B on its top is also started. In this case, when Application A is restarted, Application B (and all applications on top of Application A) will exit. 
```
[DTContextGet() startApplication:@"A 的 appid" params:@{@"arg": @"something"} launchMode:kDTMicroApplicationLaunchModePushWithAnimation];
```
Meanwhile, DTMicroApplicationDelegate of Application A will receive the following event, with arguments carried in options. 
```
- (void)application:(DTMicroApplication *)application willResumeWithOptions:(NSDictionary *)options
{
}
```
