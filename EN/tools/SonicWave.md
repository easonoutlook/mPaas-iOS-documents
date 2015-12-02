@cnName SonicWaveNFC Component
@priority 8

# SonicWaveNFC Component

[TOC]

## 1 Usage and function

The sonic-wave near field communication technology enables terminals with speakers and microphones to achieve data communication with the sonic wave. 

The technology does not require modification to the hardware. It is fully dependent on the software, featuring a low deployment cost, and is thus capable of rapid promotion.  Its applicable scenarios include but are not limited to the following: 

* Establishing dialogs between mobile phones to achieve face-to-face payment and collection. 

* Establishing dialogs between mobile phones and cash register PCs (or tablets) to achieve face-to-face payment and collection. 

* Establishing dialogs between mobile phones and PCs to pay for online goods on the mobile phones. 

* Establishing dialogs between mobile phones and check-in PCs (or check-in modules) of the business for precise check-ins. 

* Bluetooth pairing between mobile phones. 

* Establishing dialogs between mobile phones to exchange name cards, photos, music files, etc. 

SonicWaveNFC SDK (SDK for short hereinafter) is a tool for developing applications of the sonic-wave near field communication technology.  It provides the best features for developing innovative mobile Internet applications and solutions. 

## 2 Communication means

The application adopts a 'half-duplex' means for communication which allows two-way data transmission between two terminals.  The two terminals cannot send data at the same time. When one terminal is sending data, the other can only wait until the first terminal finishes the sending before the second terminal is able to send data. 
The figure below illustrates the processing procedure of the application during a communication: 
![Screen Shot 2015-05-26 at 10.37.02 AM](https://os.alipayobjects.com/rmsportal/ooxArttfVoYDnnR.png)

The sender is terminal using the speaker to send acoustic signals, and the recipient is the terminal using the microphone to record the acoustic signals. 

** The procedure of the sender is as follows: **

1. The application system calls the method to start sending data;

2. The application will process data sending in the background thread in loop execution;

3. The application system calls the method to stop sending data in the event of a timeout or other application events; 

4. The application receives the instruction for stopping sending data in the background thread, and stops execution;

5. The data transmission process is stopped. 

** The procedure of the receiver is as follows: **

1. The application system calls the method to start receiving data;

2. The application will process data receiving in the background thread in loop execution;

3. If the data is successfully received when the application is executing the data receiving procedure, the execution will stop. The application invokes an event callback for successful data receiving, and the data receiving process is ended; 

4. If the data receiving action times out when the application is executing the data receiving procedure, the execution will stop. The application invokes an event callback for data receiving timeout, and the data receiving process is ended;

5. The application system calls the method to stop receiving data in the event of other application events;

6. The application receives the instruction for stopping receiving data in the background thread, and stops execution;

7. The data receiving procedure is ended. 


## 3 How to use

The sonic-wave communication component is MPSonicWaveNFC. Check it during packaging for generating the mPaas.framework. You also need to add mPaas.bundle to the project as it contains the default sound effect files. 

![Screen Shot 2015-05-26 at 10.37.02 AM](https://t.alipayobjects.com/images/rmsweb/T1K90gXX0oXXXXXXXX.png)

### 3.1 Sending data

Among them, the AppObj is the instance of a container class of the application system or a page object.

![Screen Shot 2015-05-26 at 10.37.02 AM](https://t.alipayobjects.com/images/rmsweb/T1xEtgXghaXXXXXXXX.png)

### 3.2 Receiving data

Among them, the AppObj is the instance of a container class of the application system or a page object.

![Screen Shot 2015-05-26 at 10.37.02 AM](https://t.alipayobjects.com/images/rmsweb/T1L_8gXoJcXXXXXXXX.png)

## 4 Interface description

### 4.1 SonicWaveNFC

#### hasHeadset
```
Description: It judges whether the headset has been inserted. 

Return: Whether the headset has been inserted.
```

#### startSendData
```
Description: Starts to send data. 

Argument: 

strData The data to be sent, 64 in number at most. When this value is empty or null, only the background sound will be sent without any data. 

iTimeoutSeconds The timeout seconds for data sending. Recommended value: 5 seconds. 

iSoundType The sound type when data is being sent. 

NFC_SENDDATA_SOUNDTYPE_SILENT	0	Silent

NFC_SENDDATA_SOUNDTYPE_NOISY	1	Sound

NFC_SENDDATA_SOUNDTYPE_MIX		2	Mixed background prompt tones

iVolume Sound volume for data sending. Valid value: 0-100. Recommended value: 80. 

handler The callback handler of SonicWaveNFC and the implementation object of SonicWaveNFCHandler. 

Return: Whether the start of data sending is normal. 

Prompt: When the data sent are all digits or lower-case letters, the transmission is quicker. 
```

#### stopSendData
```
Description: Stops sending data. 
```

#### setBkSoundWave4MixFromRes
```
Description: Sets the mixed background prompt tones used. 

Argument:

resName Resource name. See the resName argument of pathForResource. 

ofType Resource type (extension). See the ofType argument of pathForResource. 

Return: Whether the settings are successful. 
```

#### startReceiveData
```
Description: Starts to receive data. 

Argument:

iTimeoutSeconds The timeout seconds for receiving data. 

iMinAmplitude The minimal threshold value of amplitude for receiving data. Audio contents lower than this value will be ignored. Recommended value: 20. 

handler The callback handler of SonicWaveNFC and the implementation object of SonicWaveNFCHandler.

Return: Whether the start of data receiving is normal. 
```

#### stopReceiveData
```
Description: Stops receiving data. 
```

### 4.2 SonicWaveNFCHandler

#### onSendDataStarted
```
Description: Starts data sending normally. 
```

#### onSendDataTimeout
```
Description: Data sending times out. 
```

#### onSendDataFailed
```
Description: Data sending failed. 

Argument:
	iErrorCode The reason code for the failure. 
```
    
#### onReceiveDataStarted
```
Description: Starts data receiving normally.
```

#### onDataReceived
```
Description: Data received. 

Argument:
	strData Data received. 
```

#### onReceiveDataTimeout
```
Description: Data receiving times out. 
```

#### onReceiveDataFailed 
```Description: Data receiving failed. 

Argument:
	iErrorCode The reason code for the failure.
```
    
#### onHeadsetOn 
```
Description: The headset is inserted while data sending or receiving is in process.  
```

#### onHeadsetOff 
```
Description: The headset is pulled out while data sending or receiving is in process.  
```
