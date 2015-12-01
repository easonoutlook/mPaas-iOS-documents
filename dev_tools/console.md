@cnName 5 命令行工具
@priority 5

# 5 命令行工具

[TOC]

安装开发者工具后，除了工程模板、文件模板、Xcode插件外，还有一套命令行工具。

## 5.1 更新或重新安装开发者工具

在终端运行下面命令，可以强行重新安装最新版的开发者工具。

```
mpaas kitupdate
```

## 5.2 创建应用

进入目的路径，使用下面命令创建一个名字为 ___XXXX___ 的工程
```Shell
mpaas createapp XXXX
```

这个工程可以直接运行，并且提供了一个 Demo 子应用和服务的例子，整个 App 是基于多 tab。第三方 App 可以在这上面进行开发，并且使用 Xcode 插件修改使用的模块。

![Image 4-22-15 at 5.09 PM](https://t.alipayobjects.com/images/rmsweb/T1Yo0fXdNXXXXXXXXX.jpg)

## 5.3 查看SDK版本信息

在终端运行下面命令，会打印出本地的 SDK 里模块的信息。

```
mpaas sdk
```
其中 `Local` 为模块在本地的版本号，`Remote` 为仓库中最新的版本号，`Missing` 表示这个模块在本地还没下载，青色底表示这个模块与仓库的版本不一致。
![image](https://os.alipayobjects.com/rmsportal/DLuXQrDMFPoIIQW.png)


## 5.4 更新本地SDK

在终端运行

```
mpaas sdk update
```
会同步本地与仓库的SDK，未下载的模块会自动下载，已经下载的模块会更新到最新版本。
![image](https://os.alipayobjects.com/rmsportal/iwKVnIuIRKWsNsU.png)

## 5.5 查看某个模块的本地版本与更新信息

在终端运行，___XXXX___ 为模块名

```
mpaas sdk XXXX
```
![image](https://os.alipayobjects.com/rmsportal/NciODqQOwnLysDg.png)

## 5.6 升级某个模块到最新版本

在终端运行，___XXXX___ 为模块名。这条命令会重新下载模块，即使本地已经是最新版本。

```
mpaas sdk update XXXX
```
![image](https://os.alipayobjects.com/rmsportal/xKembRCaBDkjyTu.png)

## 5.7 下载或重新下载某个模块

在终端运行，___XXXX___ 为模块名。这条命令会重新下载模块，即使本地已经存在。

```
mpaas sdk download XXXX
```
![image](https://os.alipayobjects.com/rmsportal/kUlPNLSfbJxZfXa.png)

## 5.8 查看开发者工具版本

你可以在 Xcode 里，mPaaS 插件菜单中看到开发者工具版本，如果有更新，会提示。
![image](https://os.alipayobjects.com/rmsportal/KprSmOoKgPAuegC.png)

也可以运行命令行查看到前开发者工具的版本

```
mpaas kitversion
```

![image](https://os.alipayobjects.com/rmsportal/qmHnxsERPKOWEZL.png)