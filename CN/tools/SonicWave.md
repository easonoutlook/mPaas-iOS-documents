@cnName 声波通讯组件
@priority 8

# 声波通讯组件

[TOC]

## 1 用途与功能

声波近场通讯技术，使具备喇叭、麦克风的终端实现了通过声波进行数据通讯的功能。

声波近场通讯技术不需要改造硬件，采用纯软件方式，部署成本低，能够快速推广。可广泛应用在各个应用场景中，示例如但不限于：

* 手机与手机之间建立会话，实现现场支付的收款、付款功能；

* 手机与收银 PC（或平板电脑）之间建立会话，实现现场支付的收款、付款功能；

* 手机与 PC 之间建立会话，实现通过手机支付网购货款的功能；

* 手机与商户的签到 PC（或签到模块）之间建立会话，实现精准签到功能；

* 手机与手机的蓝牙配对；

* 手机与手机之间建立会话，交换名片、照片、音乐等信息。

SonicWaveNFC SDK（后简称 SDK）是为开发声波近场通讯技术相关应用提供的开发工具。为开发创新性的移动互联应用、解决方案提供了最佳的功能。

## 2 通讯方式

程序采用`半双工（ half-duplex）`方式通讯，允许两台终端之间的双向数据传输。同一时间只由一台终端发送数据，若另一台终端要发送数据，需等原来发送数据的终端发送完成后再处理。
下图表示了程序进行一次通讯时的处理过程：
![Screen Shot 2015-05-26 at 10.37.02 AM](https://os.alipayobjects.com/rmsportal/ooxArttfVoYDnnR.png)

发送方为使用喇叭发出声波信号发送数据的终端，接收方为使用麦克风录取声波信号接收数据的终端。

** 发送方的处理过程如下：**

1、应用系统调用开始发送数据方法；

2、程序将在后台线程中执行发送数据处理，循环执行；

3、应用系统在超时或其他应用事件触发时，调用停止发送数据方法；

4、程序在后台线程中收到停止发送数据指令，停止执行；

5、结束发送数据过程。

** 接收方的处理过程如下：**

1、应用系统调用开始接收数据方法；

2、程序在后台线程中执行接收数据处理，循环执行；

3、若程序在执行接收数据处理时，数据被成功接收，则停止执行，向应用系统发出数据被成功接收的事件回调，结束接收数据过程；

4、若程序在执行接收数据处理时，数据接收超时，则停止执行，向应用系统发出数据接收超时的事件回调，结束接收数据过程；

5、应用系统在其他事件应用事件触发时，调用停止接收数据方法；

6、程序在后台线程中收到停止接收数据指令，停止执行；

7、结束接收数据过程。


## 3 使用方法

声波通讯组件为 MPSonicWaveNFC，打包时勾选后生成 mPaas.framework，同时需要将 mPaas.bundle 添加到工程中，里面有默认的音效文件。

![Screen Shot 2015-05-26 at 10.37.02 AM](https://t.alipayobjects.com/images/rmsweb/T1K90gXX0oXXXXXXXX.png)

### 3.1 发送数据

其中，AppObj 为应用系统中某容器类的实例或某页面对象。
![Screen Shot 2015-05-26 at 10.37.02 AM](https://t.alipayobjects.com/images/rmsweb/T1xEtgXghaXXXXXXXX.png)

### 3.2 接收数据

其中， AppObj 为应用系统中某容器类的实例或某页面对象。
![Screen Shot 2015-05-26 at 10.37.02 AM](https://t.alipayobjects.com/images/rmsweb/T1L_8gXoJcXXXXXXXX.png)

## 4 接口说明

### 4.1 SonicWaveNFC

#### hasHeadset
```
描述：判断外放耳麦是否插入

返回：外放耳麦是否插入
```

#### startSendData
```
描述：开始发送数据

参数：

strData 需要发送的数据，最长 64 个，该值为空或 null 时，则只发背景音，不发送数据

iTimeoutSeconds 发送数据的超时秒数，建议值为 5 秒

iSoundType 发送数据时的发声模式

NFC_SENDDATA_SOUNDTYPE_SILENT	0	无声模式

NFC_SENDDATA_SOUNDTYPE_NOISY	1	有声模式

NFC_SENDDATA_SOUNDTYPE_MIX		2	混合背景提示音模式

iVolume 发送数据时的音量，有效值为 0-100，建议值为 80

handler 声波近场通讯的回调处理接口 SonicWaveNFCHandler 实现对象

返回：开始发送数据是否正常

提示：当发送的数据都为数字、小写字母时，传输速度更快。
```

#### stopSendData
```
描述：停止发送数据
```

#### setBkSoundWave4MixFromRes
```
描述：设置使用的混合背景提示音

参数：

resName 资源名称，参见 pathForResourc 的同名参数

ofType 资源类型（扩展名），参见 pathForResource 的同名参数

返回：是否设置成功
```

#### startReceiveData
```
描述：开始接收数据

参数：

iTimeoutSeconds 接收数据的超时秒数

iMinAmplitude 接收数据时幅度值的最小门限值，低于该门限值的音频内容将被忽略，建议值为 20

handler 声波近场通讯的回调处理接口 SonicWaveNFCHandler 实现对象

返回：开始接收数据是否正常
```

#### stopReceiveData
```
描述：停止接收数据
```

### 4.2 SonicWaveNFCHandler

#### onSendDataStarted
```
描述：发送数据正常开始
```

#### onSendDataTimeout
```
描述：发送数据超时
```

#### onSendDataFailed
```
描述：发送数据失败

参数：
	iErrorCode 失败的原因码
```
    
#### onReceiveDataStarted
```
描述：接收数据正常开始
```

#### onDataReceived
```
描述：接收数据成功

参数：
	strData 接收到的数据
```

#### onReceiveDataTimeout
```
描述：接收数据超时
```

#### onReceiveDataFailed 
```描述：接收数据失败

参数：
	iErrorCode 失败的原因码
```
    
#### onHeadsetOn 
```
描述：发送数据或接收数据过程中，外放耳麦被插入了
```

#### onHeadsetOff 
```
描述：发送数据或接收数据过程中，外放耳麦被拔出了
```