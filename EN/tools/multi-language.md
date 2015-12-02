@cnName Multilingual Component
@priority 9


# Multilingual Component

[TOC]

## 1 Introduction

MPLanguageSetting is used for switching the language environments in the application. It is independent from the system language settings.  It has the following features:

1. It switches the constant character languages automatically and the caller does not need to care about it. 
2. For parameterized strings, it offers callback methods to the caller.  
3. With further special demands, you can monitor the notifications issued by mPaaS at language switching.  

## 2 How to use

### 2.1 Switching monolingual application to monolingual one

1. Import mPaas.framework and MPLanguage.bundle with the resource files into the project. This bundle is located in the mPaas.framework directory and should be copied from the application.  
2. Use NSLocalizedString macro for compiling the strings to be localized in the codes so that the .strings file can be generated, for example: 

```
//  The recommended key value should be in the "module.content" format. Do not use "content" directly. This will help to distinguish between different modules.  

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    CustomTableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"kReuseCell" forIndexPath:indexPath];

    cell.titleLabel.text  = NSLocalizedString(@"app1.table.cell.title", @"comment here");
    cell.subTitleLabel.text = NSLocalizedString(@"app1.table.cell.subTitle", @"comment here");
    cell.textField.placeholder = NSLocalizedString(@"app1.table.cell.textfield", @"comment here");

    return cell;
}
```

Extract texts to be localized with the genstrings method, and generate the .strings file.  

```
// Open the terminal, find the project root directory and execute: 

find ./ -name "*.m" -print0 | xargs -0 genstrings -o . 
```

3.After the execution is done, the Localizable.strings file will be generated in the current directory. The content is similar to the following:

```
/* comment here. */
"app1.table.cell.subTitle" = "app1.table.cell.subTitle";

/* comment here. */
"app1.table.cell.textfield" = "app1.table.cell.textfield";

/* comment here. */
"app1.table.cell.title" = "app1.table.cell.title";
```

4.Copy the content in Localizable.strings to the .strings file at MPLanguage.bundle/, and translate the content into the correct language. For example, copy the content in Localizable.strings to MPLanguage.bundle/zh_CN.strings (we will provide tools to help you with this step), and translate it into the Chinese language: 

```
// Filename: zh_CN.strings 

/*  comment here. */
"app1.table.cell.subTitle" = "Subtitle";

/* comment here. */
"app1.table.cell.textfield" = "Click and enter";

/* comment here. */
"app1.table.cell.title" = "Title";
```

5.Evoke the language settings page. There are two ways to achieve this: (1) Using the startApp method. (2) Using the langauge settings VC provided by MPLanguageSetting.  

(1) Using the startApp method: 
> Add 'MPLanguageSettingrAppDelegate' in MobileRuntime.plist and set the AppID. 
 ![Snip20150714_1](http://aligitlab.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/mpaas-ios/demos/1b028ecaa6/Snip20150714_1.png)

> Call the method at where you want to evoke the language settings page.    ` [DTContextGet() startApplication:@"20000003" params:@{} animated:YES];`

(2) Using the 'MPLanguageManageController':   

```
MPLanguageManageController *settingVC = [[MPLanguageManageController alloc] init];
// push  settingVC Or present settingVC
```

At this point, the project is already able to handle the language switching for constant strings.  

## 3 Advanced

1. Get the text content in the current language setting environment: 

	```
	//Use the macro __TEXT(key, comment) provided by MPLanguageSettings.  

	- (void)tabBarController:(UITabBarController *)tabBarController didSelectViewController:(UIViewController *)viewController
	{
    	self.title = __TEXT(viewController.title,@""); // Use the viewController.title as the key value and read the corresponding text content in the .strings file of the current language. 
	}
	```

2. Implementation`- (void)languageSettingDidChange:(NSDictionary *)info   NS_REQUIRES_SUPER ;`. 

	```
	//The DTViewController class realizes the monitoring of notifications on language switching, and its subclasses are capable of handling layout update and resource replacement during language switching with the 'languageSettingDidChange' method. 

	// DemoAppViewController.m 

	- (void)languageSettingDidChange:(NSDictionary *)info
	{
    	[super languageSettingDidChange:info];
   	 if (![info[APLanguageSettingInfoNewKey] isEqualToString:info[APLanguageSettingInfoOldKey]]) {
        self.title = __TEXT(@"app.sub.app1", @"Sub application");
        	// update constraints 
        	// update image resources , etc. 
   	 	}
    	NSLog(@"DemoAppVC");
	}
	```

3. Monitor notifications on language switching:  

| Field   |      Implication      | Remark |
|----------| ----------|-------------|
| APLanguageSettingDidChangeNotification |  Name of the notification on language switching |  | 
| APLanguageSettingInfoOldKey |  The acquired key of the old language in userInfo in the notification |  | 
| APLanguageSettingInfoNewKey |  The acquired key of the new language in userInfo in the notification | The new language name may be the same as the old one|
