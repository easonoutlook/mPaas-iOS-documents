@cnName 5 Command Line Tool 
@priority 5

# 5 Command Line Tools

[TOC]

After Developer Tools installed, there is a set of Command Line Tools beside Project Template, File Template and Xcode Plug-in.

## 5.1 Update or Re-install Developer Tools

Run the command below on the terminal to re-install the latest Developer Tools.

```
mpaas kitupdate
```

## 5.2 Create APP

Enter the target directory and execute the command below to create a project named XXXX
```Shell
mpaas createapp XXXX
```

You can directly run this project which includes a Demo APP and service instance based on multi-tab. You can develop APPs on base of this demo and change the modules using Xcode Plug-in. 

![Image 4-22-15 at 5.09 PM](https://t.alipayobjects.com/images/rmsweb/T1Yo0fXdNXXXXXXXXX.jpg)

## 5.3 Check SDK Version Information

Run the command below on the terminal to print out the module information of local SDK.

```
mpaas sdk
```

In the result, `Local` indicates the version of local module; `Remote` indicates the latest version in repository; `Missing` means this module hasn’t been downloaded to local machine; and the information in cyan background means the version of module is inconsistent with that in repository.
![image](https://os.alipayobjects.com/rmsportal/DLuXQrDMFPoIIQW.png)


## 5.4 Update Local SDK

Run the command below on the terminal

```
mpaas sdk update	
```
Then local SDK will be synchronized with that in repository, i.e. the missing modules will be downloaded and the exising modules wil lbe updated to the latest version.
![image](https://os.alipayobjects.com/rmsportal/iwKVnIuIRKWsNsU.png)

## 5.5 Check Local Version and Update Information of a Certain Module

Run the command below on the terminal, replace xxxx with the module name.

```
mpaas sdk XXXX
```
![image](https://os.alipayobjects.com/rmsportal/NciODqQOwnLysDg.png)

## 5.6 Update a Certain module to the Latest Version

Run the command below on the terminal, replace xxxx with the module name. The  module will be re-downloaded regardless of current version.

```
mpaas sdk update XXXX
```
![image](https://os.alipayobjects.com/rmsportal/xKembRCaBDkjyTu.png)

## 5.7 Download or Re-download a Certain Module

Run the command below on the terminal, replace xxxx with the module name. The module will be re-downloaded even it already exists in local.

```
mpaas sdk download XXXX
```
![image](https://os.alipayobjects.com/rmsportal/kUlPNLSfbJxZfXa.png)

## 5.8 Check Version of Developer Tools

The version of Developer Tools can be found in mPaaS menu of Xcode; if update is available, a prompt will appear.
![image](https://os.alipayobjects.com/rmsportal/KprSmOoKgPAuegC.png)

Current version of Developer Tools can also be found by running the command below.

```
mpaas kitversion
```

![image](https://os.alipayobjects.com/rmsportal/qmHnxsERPKOWEZL.png)
