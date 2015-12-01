@cnName 多语言组件
@priority 9

# 多语言组件

[TOC]

## 1 简介

MPLanguageSetting 用于实现 App 内语言环境切换，不依赖于系统语言设置。具有以下特点：

1. 可无需调用方关心地自动切换常量字符的语言.
2. 对于参数化的字符串,提供给调用方回调方法. 
3. 如还有特殊需求，也可以监听语言切换时框架发出的通知. 

## 2 使用方法

### 2.1 将单一语言应用切换为多语言

1. 将 mPaas.framework，以及资源文件 MPLanguage.bundle 引入工程文件中，这个 bunlde 在 mPaas.framework 目录下，需要应用拷贝出来。 
2. 代码中需要本地化的字符串使用 NSLocalizedString 宏编写，以便于生成.strings 文件. 如:

```
//  建议 key 值使用 "module.content " 这种形式,而不要直接用 "content" , 以便于模块之间的区分. 

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    CustomTableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"kReuseCell" forIndexPath:indexPath];

    cell.titleLabel.text  = NSLocalizedString(@"app1.table.cell.title", @"comment here");
    cell.subTitleLabel.text = NSLocalizedString(@"app1.table.cell.subTitle", @"comment here");
    cell.textField.placeholder = NSLocalizedString(@"app1.table.cell.textfield", @"comment here");

    return cell;
}
```

使用 genstrings 抽取代码中需要本地化的文本，生成.strings 文件. 

```
// 打开终端, 找到工程所在目录根目录下 执行：

find ./ -name "*.m" -print0 | xargs -0 genstrings -o . 
```
3.执行完成后,会在当前目录下生成一个 Localizable.strings 文件 . 内容类似如下:
```
/* comment here. */
"app1.table.cell.subTitle" = "app1.table.cell.subTitle";

/* comment here. */
"app1.table.cell.textfield" = "app1.table.cell.textfield";

/* comment here. */
"app1.table.cell.title" = "app1.table.cell.title";
```

4.将 Localizable.strings 文件中的内容复制到 MPLanguage.bundle/下的.strings 文件下, 并翻译成对应语言. 如,将 Localizable.strings 文件内容复制到 MPLanguage.bundle/zh_CN.strings 中(后续我们会提供工具帮助完成),并翻译成中文:

```
// Filename: zh_CN.strings 

/*  comment here. */
"app1.table.cell.subTitle" = "子标题";

/* comment here. */
"app1.table.cell.textfield" = "点击后输入";

/* comment here. */
"app1.table.cell.title" = "标题";
```

5.唤起语言设置页面. 有两种方式: （ 1）通过 startApp 方式， （ 2） 通过 MPLanguageSetting 提供的语言设置 VC 

（ 1）通过 startApp 方式:
> 在 MobileRuntime.plist 中添加一个 `MPLanguageSettingrAppDelegate`,并设置 AppID
 ![Snip20150714_1](http://aligitlab.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/mpaas-ios/demos/1b028ecaa6/Snip20150714_1.png)

> 在需要唤起设置语言页面的位置调用   ` [DTContextGet() startApplication:@"20000003" params:@{} animated:YES];`

（ 2）使用提供的`MPLanguageManageController`方式:  

```
MPLanguageManageController *settingVC = [[MPLanguageManageController alloc] init];
// push  settingVC 或 present settingVC
```

至此，工程已经可以自动处理常量字符串的语言切换了. 

## 3 进一步使用

1. 获得当前语言设置环境下的文本内容:

```
//使用 MPLanguageSettings 提供的宏 __TEXT(key, comment)

- (void)tabBarController:(UITabBarController *)tabBarController didSelectViewController:(UIViewController *)viewController
{
    self.title = __TEXT(viewController.title,@""); // 以 viewController.title 为 key 值,到当前语言对应的 .strings 文件中读取对应的文本内容.
}
```


2. 实现`- (void)languageSettingDidChange:(NSDictionary *)info   NS_REQUIRES_SUPER ;`. 

```
//DTViewController 类中实现了对语言切换通知的监听, 子类可以实现 `languageSettingDidChange`方法来处理语言切换时的布局更新、资源替换等

// DemoAppViewController.m 

- (void)languageSettingDidChange:(NSDictionary *)info
{
    [super languageSettingDidChange:info];
    if (![info[APLanguageSettingInfoNewKey] isEqualToString:info[APLanguageSettingInfoOldKey]]) {
        self.title = __TEXT(@"app.sub.app1", @"子 App");
        // update constraints 
        // update image resources , etc. 
    }
    NSLog(@"DemoAppVC");
}
```

3. 监听语言切换时的通知: 

| 字段   |      意义      | 备注 |
|----------| ----------|-------------|
| APLanguageSettingDidChangeNotification |  语言切换时的通知名 |  | 
| APLanguageSettingInfoOldKey |  通知中 userInfo 获取原语言名的 key |  | 
| APLanguageSettingInfoNewKey |  通知中 userInfo 获取新的语言名的 key | 新的语言名可能与原语言名一致|