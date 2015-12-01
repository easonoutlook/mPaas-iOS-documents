@cnName 4 Xcode插件
@priority 4

# 4 Xcode插件

[TOC]

开发者工具包括 Xcode 插件，安装完成后会自动出现在 Xcode > Edit 菜单下。目前提供的功能如图：
![image](https://os.alipayobjects.com/rmsportal/KfzNnsKkzqxjcoW.png)

## 4.1 移动模块管理器

菜单项为`Modules`，点击后出现如下界面：

![image](https://os.alipayobjects.com/rmsportal/wXMUTAhCjtgsGWm.png)

`Install`  安装移动模块

`Modify`  修改已经安装了移动的目标使用的模块

`Uninstall`  卸载移动模块

一个Xcode工程里可能有很多编译 Targets ，插件支持对 Target 进行单独的设置。当工程里有多个 Target 时会出现选择框：

![image](https://os.alipayobjects.com/rmsportal/jgMjrDUEBMqzbeY.png)

选择需要操作的 Target 后进入模块编辑窗口：
![image](https://os.alipayobjects.com/rmsportal/FghyLxaAYtnJpTt.png)

勾选需要使用的移动模块，并选择相应的版本后，点击 OK 即可。

`红色箭头`本次编辑修改变更过的模块。

`问号按钮`显示模块的版本和升级信息。

`Copy frameworks to project path`移动 SDK 会统一安装到用户机器的公共目录，并由插件自动维护。当不勾选时，会使用这个sdk目录里公共的包。这样可以减小工程的大小，用户只需要提交自己的代码即可。当工程在其它机器上运行时，只要也安装了移动插件，即使还没安装移动SDK，插件会自动下载SDK，保证程序正常运行。当然，你也可以勾选，这样相应模块的包会拷贝到工程的 mPaaS 目录下，你可以将这些模块包作为代码一起提交。

___mPaaS 目录会出现在工程中，不要手动修改这个目录里的内容。___
![image](https://os.alipayobjects.com/rmsportal/qPbFqgSlliJtDoR.png)

如果使用模块管理器时遇到下面错误，说明你的机器上还没有安装 `xcodeproj` 这个 Ruby Gem 或版本比较老。请参考[`概述`](./summary.md)
![image](https://os.alipayobjects.com/rmsportal/unDugLrCfUqQNEu.png)

## 4.2 无线保镖密钥图片生成器

菜单项为`Security Guard Editor`，界面如图：
![image](https://os.alipayobjects.com/rmsportal/pFjLIjPvIOVCpLd.png)

点击右上角的`+`, `-`添加或删除密钥对，直接在列表里编辑内容。密钥图片需要与应用的`Bundle Identifier`相匹配。勾选`Replace file of current project`会在生成图片后自动替换本工程内的`yw_1222.jpg`图片。

## 4.3 微应用与服务编辑器

菜单项为`App & Service Editor`，在使用移动客户端框架的工程内点击后，会唤起编辑器。方便开发者浏览已经注册的微应用或微服务，并提供可视化编辑功能。
![image2](https://t.alipayobjects.com/images/rmsweb/T13MliXm0jXXXXXXXX.png)

## 4.4 统一存储DAO功能配置文件生成

菜单项为`DAO Config Generator`，在代码编辑器中选中需要生成DAO配置文件的类声明。
![image3](https://t.alipayobjects.com/images/rmsweb/T1K4hiXe4cXXXXXXXX.png)

点击`DAO Config Generator`，会自动生成DAO存储配置文件。文件会创建在临时目录中，复制到工程目录下并添加到工程中即可。关于统一存储DAO功能的使用方法请参考[`工具组件集->统一存储`](../tools/data_center/summary.md)
![image4](https://t.alipayobjects.com/images/rmsweb/T133hiXfJkXXXXXXXX.png)

## 4.5 其它插件

### 4.5.1 Open Sandbox

快速打开应用模拟器沙箱

### 4.5.2 Copy-Paste Detector

基于CPD的代码重复检测工具

### 4.5.3 Open Shell

快速打开终端，并定位到当前工程的路径
