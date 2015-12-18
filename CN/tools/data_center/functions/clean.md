@cnName 2.6 数据清理
@priority 6

## 2.6 数据清理

### 2.6.1 自动清理的缓存目录

创建一个可以自动维护容量的缓存目录，通过 ___APPurgeableType___ 指定清空的逻辑，通过 ___size___ 指定缓存目录的大小。应用每次启动会在后台进程检查目录状态，并按需求删除文件。如果一个目录设置了容量上限，当达到上限时，会删除其中创建时间最老的文件，使目录恢复到1/2容量上限的使用情况。

```C
#import <Foundation/Foundation.h>

typedef NS_ENUM(NSUInteger, APPurgeableType)
{
    APPurgeableTypeManual = 0,          // 当用户手动清除缓存时清空
    APPurgeableTypeThreeDays = 3,       // 自动删除三天前的数据
    APPurgeableTypeOneWeek = 7,         // 自动删除一周前的数据
    APPurgeableTypeTwoWeeks = 14,       // 自动删除两周前的数据
    APPurgeableTypeOneMonth = 30,       // 自动删除一个月前的数据
};

#ifdef __cplusplus
extern "C" {
#endif // __cplusplus
    
    /**
     *  根据用户的输入返回一个可被清理的存储路径，同时会自动判断目录是否存在，如果不存在会创建。
     *
     *  @param userPath 用户指定的路径，比如之前使用"Documents/SomePath"来拼接，现在使用APPurgeableStoragePath(@"Documents/SomePath")获得路径即可。
     *  @param type 指定自动清空的类型，可以是用户手动或每周，或每三天。
     *  @param size 指定当尺寸达到多大时，清空较老的数据，单位MB。0表示不设置上限。
     *
     *  @return 目标路径
     */
    NSString* APPurgeablePath(NSString* path);
    NSString* APPurgeablePathType(NSString* path, APPurgeableType type);
    NSString* APPurgeablePathTypeSize(NSString* path, APPurgeableType type, NSUInteger size /* MB */);
    
    /**
     *  清空并重置所有注册的目录
     */
    void ResetAllPurgeablePaths();
    
#ifdef __cplusplus
}
#endif // __cplusplus
```

### 2.6.2 缓存清理接口

统一存储提供清理缓存的实现类，这个类从 ___PurgeableCache.plist___ 中读取清理任务，这个文件需要放在应用的 ___Main Bundle___里。清理器会异步执行。回调函数总会在主线程调用，可以在里面进行 UI 展示与处理。

```
#import <Foundation/Foundation.h>

typedef NS_ENUM(NSUInteger, APCacheCleanPhase)
{
    APCacheCleanPhasePreCalculating = 0,    // 执行前扫描沙箱大小
    APCacheCleanPhaseCleaning,              // 正在清理
    APCacheCleanPhasePostCalculating,       // 执行完成扫描沙箱大小
    APCacheCleanPhaseDone,                  // 完成
};

@interface APUserCacheCleaner : NSObject

/**
 *  异步执行清理。必须传一个回调方法。
 *  当phase返回APCacheCleanPhasePreCalculating，APCacheCleanPhaseCleaning，APCacheCleanPhasePostCalculating时，progress代表真实的进度。最大为1.0。
 *  当phase为APCacheCleanPhaseDone时，progress返回清理了多少MB的数据。
 *
 *  @param callback 回调方法
 */
+ (void)execute:(void(^)(APCacheCleanPhase phase, float progress))callback;

@end
```

在 ___PurgeableCache.plist___ 中可以定义两种类型的清理任务。
![image](https://os.alipayobjects.com/rmsportal/DSOmpFvqYRVSCjw.png)

___Path___ 文件或目录的路径，只需要沙箱内的相对路径即可。

___Entries___ 当 ___Path___ 为一个目录时，删除它下面的哪些子文件或目录，支持 ___*___ 进行通配。

___Class___ 指定一个回调方法的定义类。

___Selectors___ 调用 ___Class___ 类的哪些___类___方法。注意，必须为类方法，不能为实例方法。