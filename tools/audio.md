@cnName 音频录制播放
@priority 2

# 音频录制播放

[TOC]

## 1 ALPAudioRecord
```C
@interface ALPAudioRecord : NSObject <AudioRecordFinishDelegate>

//音量改变定时器
//@property(nonatomic, retain) NSTimer *voicePowerTimer;

//录音数据
@property(nonatomic, retain) NSData *cafData;

//最长录音时间，默认 30 秒
@property(nonatomic, assign) double maxDuration;

//最短录音时间，默认 1 秒
@property(nonatomic, assign) double minDuration;

//创建时间
@property(nonatomic, retain) NSString *createAt;

//filePath 为空，代表不需要存 file.
@property(nonatomic, retain) NSString *filePath;

@property(nonatomic, assign) id <ALPAudioRecordDelegate> delegate;

//设为 YES，转码及存储将在子线程里去做(delegate 仍在主线程回调)；为保证兼容性，默认为 NO，所有操作均在主线程里做；
//建议使用 YES；
@property(nonatomic, assign) BOOL recordAsync;

/**
 *  启动录制
 *  @param filePath 录音完后保存的路径，需要带文件名，并且当目录存在时才能录制成功。filePath 为空表示不需要存 file。
 *  @return 带有语音文件地址的语音类
 *
 */
- (void)startRecordSavedInFile:(NSString *)filePath;

/**
 *  取消录制
 */
- (void)cancelRecord;

/**
 *  完成录音
 */
- (void)finishRecord;

/**
 *  录音权限
 */
+ (void)requestMicrophonePermission:(void (^)(BOOL granted))block;
@end
```

实现代理
```C
@protocol ALPAudioRecordDelegate <NSObject>
@optional
/**
 *  录制结束的回调
 *  录制结束的情况：
 *  1.  调用 finishRecord 完成录制
 *  2.  调用 cancelRecord 取消录制
 *  3.  录制时间超过设定的最大时间
 *  4.  录制保存的文件路径无效
 *  5.  录制失败
 */
- (void)recordFinishWithALPAudioMessage:(ALPAudioMessage *)message recordFinishState:(ALPAudioRecordFinishState)state;


/**
 * 由于录音超时，将要进行格式转换和自动存储，供业务方 UI 控制使用.
 */
- (void)recordWillFinishForTimeOut;

@end
```

## 2. ALPAudioPlayer
```C
@interface ALPAudioPlayer : NSObject

@property (nonatomic, retain) ALPAudioMessage *message;

//+ (ALPAudioPlayer *)sharedALPAudioPlayer;

/**
 *  创建 ALPAudioPlayer 的页面在 viewWillDisappear 或退出时需要调用
 */
- (void)stop;

/**
 *  切换到扬声器状态下可以触发一些 UI 的显示用来提示用户
 */
- (void)switchToSpeaker:(LWAudioRecorderCallbackBlock)block;

/**
 *  播放音频文件
 *  @param message 音频文件
 */
- (void)playWithAudioMessage:(ALPAudioMessage *)message;

/**
 *  停止播放音频文件
 *  @param message 音频文件
 */
- (void)stopWithAudioMessage:(ALPAudioMessage *)message;

/**
 *  播放音频文件
 *  @param message 音频文件
 *  @param block 播放完毕后的回调
 */
- (void)playWithAudioMessage:(ALPAudioMessage *)message stopBlock:(LWAudioPlayerStateCallbackBlock)block;

/**
 * 播放
 */
- (void)play;

/**
 *  当前音频文件是否正在播放
 */
- (BOOL)isPlayWithAudioMessage:(ALPAudioMessage *)message;

/**
 *  保持最后一次播放模式，通常在 playXXX 方法之前执行 如：最后一次是听筒，这次同样也是听筒
 */
- (void)keepLastPlayMode;

@end
```