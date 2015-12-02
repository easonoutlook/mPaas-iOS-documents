@cnName Audio Recording and Playback
@priority 2

# Audio Recording and Playback

[TOC]

## 1 ALPAudioRecord
```C
@interface ALPAudioRecord : NSObject <AudioRecordFinishDelegate>

//Volume change timer
//@property(nonatomic, retain) NSTimer *voicePowerTimer;

//Recording data
@property(nonatomic, retain) NSData *cafData;

//Maximum duration for recording. 30 seconds by default. 
@property(nonatomic, assign) double maxDuration;

//Minimum duration for recording. 1 second by default.
@property(nonatomic, assign) double minDuration;

//Create time. 
@property(nonatomic, retain) NSString *createAt;

//When filePath is empty, it indicates that no file needs to be stored. 
@property(nonatomic, retain) NSString *filePath;

@property(nonatomic, assign) id <ALPAudioRecordDelegate> delegate;

//When it is set to YES, transcoding and storage will be performed in the subthread (delegate is called back in the main thread). For compatibility considerations, it is set to NO by default and all the operations are performed in the main thread. 
//The recommended value is YES. 
@property(nonatomic, assign) BOOL recordAsync;

/**
 *  Start recording. 
 *  @param filePath The path for saving the recordings. The file name should be included and the recording will only succeed when the directory exists.  When filePath is empty, it indicates that no file needs to be stored.
 *  @return The voice type with the voice file URL. 
 *
 */
- (void)startRecordSavedInFile:(NSString *)filePath;

/**
 *  Cancel recording. 
 */
- (void)cancelRecord;

/**
 *  Complete recording. 
 */
- (void)finishRecord;

/**
 *  Recording permission. 
 */
+ (void)requestMicrophonePermission:(void (^)(BOOL granted))block;
@end
```

Implementation delegate
```C
@protocol ALPAudioRecordDelegate <NSObject>
@optional
/**
 *  Callback when recording ends. 
 *  Cases of recording ending.
 *  1.  Call finishRecord to finish recording. 
 *  2.  Call cancelRecord to cancel recording. 
 *  3.  The recording duration exceeds the maximum duration set. 
 *  4.  The path for saving the recording file is invalid. 
 *  5.  The recording fails. 
 */
- (void)recordFinishWithALPAudioMessage:(ALPAudioMessage *)message recordFinishState:(ALPAudioRecordFinishState)state;


/**
 * The recording times out and the format conversion and automatic storage operations will be performed for UI control and use by the business party. 
 */
- (void)recordWillFinishForTimeOut;

@end
```

## 2 ALPAudioPlayer
```C
@interface ALPAudioPlayer : NSObject

@property (nonatomic, retain) ALPAudioMessage *message;

//+ (ALPAudioPlayer *)sharedALPAudioPlayer;

/**
 *  The page for creating ALPAudioPlayer will be called in the viewWillDisappear function or at exit. 
 */
- (void)stop;

/**
 *  Switching to the speaker will trigger some UI display elements to prompt the user. 
 */
- (void)switchToSpeaker:(LWAudioRecorderCallbackBlock)block;

/**
 *  Play the audio file. 
 *  @param message Audio file. 
 */
- (void)playWithAudioMessage:(ALPAudioMessage *)message;

/**
 *  Stop playing the audio file. 
 *  @param message Audio file.
 */
- (void)stopWithAudioMessage:(ALPAudioMessage *)message;

/**
 *  Play the audio file.
 *  @param message Audio file.
 *  @param block Callback after playing is finished. 
 */
- (void)playWithAudioMessage:(ALPAudioMessage *)message stopBlock:(LWAudioPlayerStateCallbackBlock)block;

/**
 * Play.
 */
- (void)play;

/**
 *  Whether the current audio file is being played. 
 */
- (BOOL)isPlayWithAudioMessage:(ALPAudioMessage *)message;

/**
 *  Keep to the last play mode, usually executed before the playXXX method. For example, the last play mode is earphone, and this play will also in the earphone mode. 
 */
- (void)keepLastPlayMode;

@end
```
