@cnName 4 Xcode Plug-in
@priority 4

# 4 Xcode Plug-in
Developer tools contain Xcode Plug-in which appears in Xcode-Edit after installed. This plug-in provides functions as shown below. 
![image](https://os.alipayobjects.com/rmsportal/YkBdAXhxuZUyCsB.png)

## 4.1 mPaaS Modules Manager
In the mPaaS menu, click `Modules`, and the following interface appears. 
![image](https://os.alipayobjects.com/rmsportal/wXMUTAhCjtgsGWm.png)
* Install: used to install the mPaaS module.
* Modify: used to change the mPaaS module installed in a target. 
* Uninstall: used to unintall the installed mPaaS module.  

An Xcode project may contain several targets, and this plug-in can be set to each target separately. When a project contains multiple targets, a selection box like the following appears. 
![image](https://os.alipayobjects.com/rmsportal/jgMjrDUEBMqzbeY.png)

The Modules window appears after you select the target. Check the modules you want and select the versions. 
![image](https://os.alipayobjects.com/rmsportal/FghyLxaAYtnJpTt.png)

* `Red Arrows` marks the modules with changes. 
* `Question Mark Buttons` displays the module version and upgrade information. 
* `Copy frameworks to project path`:
	* The mPaaS SDK will be installed in a shared directory of the user's machine.
	* If you leave this option unchecked, the SDK package in the shared directory will be used. Thus the project size is reduced and users only need to care about their own codes. When the project is run in other machines without mobile SDK, as long as the machine has Mobile plug-in installed, the plug-in will download the mobile SDK to ensure the project run normally. 
	* If you check this option, the mPaaS SDK will be installed in the `mPaas` directory of the project and will be taken as a part of the project. 
	

__The mPaaS directory exists in the project. Do not make any change in the directory manully.__
![image](https://os.alipayobjects.com/rmsportal/qPbFqgSlliJtDoR.png)

If the following errors appear, it means no Ruby Gem of `xcodeproj` is installed in your machine or the version is too old. Please refer to [`Overview`](./summary.md) for installation. 
![image](https://os.alipayobjects.com/rmsportal/unDugLrCfUqQNEu.png)


## 4.2 Key Image Generator of Mobile Secutiry Guard 
In the mPaaS menu, click `Security Guard Editor` and the following interface appears. 
![image](https://os.alipayobjects.com/rmsportal/pFjLIjPvIOVCpLd.png)
	
Click `+` or `-` on the upper right to add or delete a key pair. You can edit the content in the list. The key image must match the `Bundle Identifier` of the application. If you check `Replace file of current project`, the generated image will replace the `yw_1222.jpg` image of the project. 


## 4.3 App & Service Editor
Select mPaaS > App & Service Editor, then click the project using moblie client SDK, the App & Service Editor will be opened. From this editor developers can view the registered micro-applications or micro-services, and edit them visually. 
![image2](https://t.alipayobjects.com/images/rmsweb/T13MliXm0jXXXXXXXX.png)

## 4.4 DAO Config Generator
In the Editor area of Xcode, select the class declarations that needs to generate DAO configuration files, and then click `DAO Config Generator` in the mPaaS menu to generate DAO configuration files automatically. The files will be created in the temporary directory. You can copy them to the project directory and add it to the project. 
![image3](https://t.alipayobjects.com/images/rmsweb/T1K4hiXe4cXXXXXXXX.png)

You can refer to [`Tool Component Set->Data Center`](../tools/data_center/summary.md) for how to use DAO. 
![image4](https://t.alipayobjects.com/images/rmsweb/T133hiXfJkXXXXXXXX.png)


## 4.5 Other Plug-ins

### 4.5.1 Open Sandbox
In the mPaaS menu, click `Open Sandbox` to quickly open the application simulator sandbox.


### 4.5.2 Copy-Paste Detector
In the mPaaS menu, click `Copy-Paste Detector` to detect the duplication of CPD-based code. 


### 4.5.3 Open Shell
In the mPaaS menu, click `Open Shell` to quickly open the client and locate the path of the current project. 

