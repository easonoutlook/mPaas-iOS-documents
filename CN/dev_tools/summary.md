@cnName 1 概述
@priority 1

# 1 概述

移动开发者工具是一套组件，包括命令行工具，Xcode插件等。辅助开发者进行开发。

在终端里运行下面命令行安装移动开发者工具
```Shell
sh <(curl -s http://code.taobao.org/svn/mpaaskit/trunk/install.sh)
```
![image](https://t.alipayobjects.com/images/rmsweb/T1e3BiXediXXXXXXXX.png)

请同时运行一下下面命令，更新一下 `Ruby Gems`

```
sudo gem update
```

保证 `xcodeproj`处于比较新的版本（2015年10月最新版本为0.28.2），才可以编辑修改最新的Xcode工程文件。运行命令：
```
gem list
```
![image](https://os.alipayobjects.com/rmsportal/RvdcdVMmtVMOqHZ.png)

___此外，如果你使用了 taobao.org 提供的 Ruby 源，需要确保使用`https`的方式。他们已经不支持`http`了。___