@cnName 2.1 APDataCenter
@priority 1

## 2.1 APDataCenter

[TOC]

### 2.1.1 简介

APDataCenter 为统一存储的入口类，为一个单例，可在代码任意地方调用
```
[APDataCenter defaultDataCenter]
```
也可以使用宏
```
#define APDefaultDataCenter [APDataCenter defaultDataCenter]
```
即会初始化 APDataCenter。

### 2.1.2 接口介绍

#### 宏定义

```
#define APDefaultDataCenter [APDataCenter defaultDataCenter]
#define APCommonPreferences [APDefaultDataCenter commonPreferences]
#define APUserPreferences [APDefaultDataCenter userPreferences]
#define APCurrentVersionStorage [APDefaultDataCenter currentVersionStorage]
```

#### 常量定义

这几个事件通知业务代码通常无须关注，但是统一存储会抛出这些通知。
```
/**
 *  前一个用户的数据库文件将要关闭的事件通知
 */
extern NSString* const kAPDataCenterWillLastUserResign;

/**
 *  用户状态已经发生切换的通知。有可能是 user 变为 nil 了，具体 userId 可以用 currentUserId 来获取。
 *  这个通知附加的 object 是个字典，如果不为 nil，里面@"switched"这个键值返回@YES 表示确实发生了用户切换事件。
 */
extern NSString* const kAPDataCenterDidUserUpdated;

/**
 *  用户并没切换，APDataCenter 重新收到登入事件。会抛这个通知。
 */
extern NSString* const kAPDataCenterDidUserRenew;
```

### 2.1.3 接口与属性

#### void APDataCenterLogSwitch(BOOL on);
```
打开或关闭统一存储的控制台 log，默认为打开。
```

#### @property (atomic, strong, readonly) NSString* currentUserId;
```
当前登录用户的 userId
```

#### + (NSString*)preferencesRootPath;
```
得到存储commonPreferences 和 userPreferences 数据库文件夹的路径。
```

#### - (void)setCurrentUserId:(NSString*)currentUserId;
```
设置当前登录的用户 Id，业务代码请不要调用，需要由登录模块调用。设置用户 ID 后，userPreferences 会指向这个用户的数据库。
```
#### - (void)reset;
```
完全重置整个统一存储目录，请谨慎。
```

#### - (APSharedPreferences*)commonPreferences;
```
与用户无关的全局存储数据库
```

#### - (APSharedPreferences*)userPreferences;
```
当前登录用户的存储数据库。不是登录态时，取到的是 nil。
```

#### - (APSharedPreferences*)preferencesForUser:(NSString*)userId;
```
返回指定用户 id 的存储对象，业务层通常使用 userPreferences 方法即可。当有异步存储需要时，防止窜数据，可以使用该方法取特定用户的存储数据库。
```

#### - (APPreferencesAccessor*)accessorForBusiness:(NSString*)business;
```
根据 business 名生成一个存取器，业务层需要自行持有这个对象。使用这个存取器后，访问 KV 存储就不需要再传 business 了。

APPreferencesAccessor* accessor = [[APDataCenter defaultDataCenter] accessorForBusiness:@"aBiz"];
[[accessor commonPreferences] doubleForKey:@"aKey"];

// 等价于

[[[APDataCenter defaultDataCenter] commonPreferences] doubleForKey:@"aKey" business:@"aBiz"];
```

#### - (APCustomStorage*)currentVersionStorage;
```
统一存储会维护一个当前版本的数据库，当版本发生升级时，这个数据库会重置。
```

#### - (id&lt;APDAOProtocol&gt;)daoWithPath:(NSString*)filePath userDependent:(BOOL)userDependent;
```
从一个配置文件生成 DAO 访问对象

@param filePath DAO 配置文件的文件路径，在 main bundle 里的文件使用下面方式：
	NSString* filePath = [[NSBundle mainBundle] pathForResource:@"file" ofType:@"xml"];
@param userDependent 指定这个 DAO 对象操作哪个数据库。如果 userDependent=NO，表示与用户无关，那么 DAO 会在 commonPreferences 的数据库文件中建表。如果 userDependent=YES，那么 DAO 对象会在 userPreferences 的数据库文件中建表。当切换用户后，后续的 DAO 操作会自动在更换后的用户文件中进行，业务无须关心用户切换。

@return 返回 DAO 对象，业务不用关心它的类名，只需要使用业务自己定义的 id<AProtocol>强制转换一下即可。返回的 DAO 对象，在需要时也可以使用 id<APDAOProtocol>进行转换，调用默认提供的方法。所以自定义的 AProtocol 不要含有 APDAOProtocol 里定义的方法。
```

#### - (id&lt;APDAOProtocol&gt;)daoWithPath:(NSString*)filePath databasePath:(NSString*)databasePath;
```
创建一个维护自己独立数据库文件的 DAO 访问对象，而不使用 APSharedPreferences。
使用 daoWithPath:userDependent:接口创建的 DAO 对象，操作的是 commonPreferences 或 userPreferences。
这个接口会创建一个 DAO 对象，并且操作的是 databasePath 指定的特定数据库文件，文件不存在会创建。
可以创建多个 DAO 对象，指定相同的 databasePath。

@param filePath     同daoWithPath:userDependent:接口
@param databasePath DAO 数据库文件的位置，可以传绝对路径，也可以传'Documents/XXXX.db'或'Library/Movie/XXX.db'这样的相对路径

@return DAO 对象
```