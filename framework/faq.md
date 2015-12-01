@cnName  5 常见问题
@priority 5

# 5 常见问题

[TOC]

### 5.1 如何使用自己的 UIApplication 代理类？

你可以声明自己的类继承 DFClientDelegate，覆盖 DFClientDelegate 的方法，并在 main.m 里使用自己的类启动应用。

### 5.2 如何退出所有微应用，回到 Launcher？

```
[DTContextGet() startApplication:@"Launcher 的 appid" params:nil animated:kDTMicroApplicationLaunchModePushNoAnimation]; 
```

### 5.3 A 应用上层有 B 应用，B 应用如何重新启动 A 应用并传递参数？

假设 A 应用已经启动，上层又启动了 B 应用，那么重新启动 A 应用会退出 B 应用（及所有 A 上层应用）
```
[DTContextGet() startApplication:@"A 的 appid" params:@{@"arg": @"something"} launchMode:kDTMicroApplicationLaunchModePushWithAnimation];
```
同时 A 应用的 DTMicroApplicationDelegate 会接收到下面事件，options 里会携带参数
```
- (void)application:(DTMicroApplication *)application willResumeWithOptions:(NSDictionary *)options
{
}
```

### 5.4 如果自定义导航条样式？

如果使用了MPCommonUI控件库，可以通过[UI样式描述文件](../tools/ui/theme/APNavigationBar.md)定义APNavigationBar的样式。如果未使用控件库，可以自行设置UINavigationBar的样式。