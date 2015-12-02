@cnName 1 Overview
@priority 1

# 1 Overview

Mobile developer Tools include a suite of components, such as command line tools, Xcode plug-in, etc. to assist developers.

To install Developer Tools, please execute the following command on a terminal 
```Shell
sh <(curl -s http://code.taobao.org/svn/mpaaskit/trunk/install.sh)
```
![image](https://t.alipayobjects.com/images/rmsweb/T1e3BiXediXXXXXXXX.png)

Please run the following command also to update `Ruby Gems`

```
sudo gem update
```

To edit the newest Xcode project files, please ensure xcodeproj is a relatively new version(the newest version is 0.28.2 in October, 2015). Execute command:
```
gem list
```
![image](https://os.alipayobjects.com/rmsportal/RvdcdVMmtVMOqHZ.png)

___Besides, if you use the Ruby source provided by taobao.org, please make sure to use HTTPS rather than HTTP which is not supported any longer. ___
