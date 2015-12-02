@cnName 3 红点提醒
@priority 3

# 3 红点提醒

[TOC]

## 3.1 简介

当客户端需要提醒用户关注某 UI 元素时，往往会在上面显示红点，红点会按照 UI 控件的级别进行累加。

移动红点提醒组件具有以下特点：

- 客户端在界面中使用的红点控件`BadgeView`具有唯一的红点标识，即`widgetId`属性；
- 红点控件支持丰富的显示样式：红点，数字，更新等，开发者也可以自定义红点控件；
- 客户端用红点管理器`BadgeManager`统一管理红点控件的显示，刷新，删除；
- 客户端在界面初始化的时候，向红点管理器注册红点控件；在销毁时，向红点管理器注销红点控件；
- 当红点管理器收到红点显示数据时，会自动刷新相应注册过的红点控件，不需要开发者额外代码控制 UI 元素；
- 红点组件之间有父子关系，父节点红点数是所有子节点红点数的和；当所有子节点红点消失时，父节点红点也不显示；

## 3.2 接口类定义

### 3.2.1 MPBadgeManager

红点管理器，负责红点控件的注册，注销，刷新以及红点数据的管理。

一、 红点管理接口

``` C
/**
 * 设置红点管理配置对象
 *
 * @param config 配置对象
 *
 * @return 无
 */
- (void)setBadgeServiceConfig:(id<MPBadgeServiceConfig>)config;

/**
 * 用户登录后，调用该接口处理红点的显示
 *
 * @param userId 当前登录用户 ID
 *
 * @return 无
 */
- (void)refreshAfterLogin:(NSString *)userId;

/**
 * 清除内存缓存的红点数据和界面上所有红点控件的显示
 *
 * @param 无
 *
 * @return 无
 */
- (void)clearAllBadges;
```

二、 红点控件使用接口

``` C
/**
 * 注册红点控件到通用红点管理模块
 *
 * @param badgeView 红点控件
 *
 * @return 无
 */
- (void)registerBadgeView:(MPAbsBadgeView *)badgeView;

/**
 * 在通用红点管理模块中，注销掉红点控件
 *
 * @param badgeView 红点控件
 *
 * @return 无
 */
- (void)unregisterBadgeView:(MPAbsBadgeView *)badgeView;

/**
 * 如果是叶子节点的红点就消除红点，否则不处理。（不用关心红点的类型）
 *
 * @param badgeView 红点控件
 *
 * @return 无
 */
- (void)tapBadgeView:(MPAbsBadgeView *)badgeView;

/**
 * 点击红点触发和该红点有关的显示处理（不用关心红点的类型）
 *
 * @param widgetId 红点控件 Id
 *
 * @return 无
 */
- (void)tapBadgeViewWithWidgetId:(NSString *)widgetId;
```

三、 红点信息获取接口

``` C
/**
 * 根据指定的红点路径 ID 获取红点信息
 *
 * @param badgeId 红点路径 ID，如:"#M_PUBLIC_RD#2013112300002181,#M_TRANS_RD#2088502524001315"
 *
 * @return 成功返回红点信息，否则返回 nil
 */
- (MPBadgeInfo *)badgeInfoWithBadgeId:(NSString *)badgeId;

/**
 * 根据指定的业务 ID 获取相关的红点总数
 *
 * @param bizId 业务 ID
 *
 * @return 指定业务相关的所有红点数
 */
- (NSUInteger)badgeCountWithBizId:(NSString *)bizId;

/**
 * 根据指定的红点控件 ID 获取相关的红点总数
 *
 * @param widgetId 红点控件 ID
 *
 * @return 指定控件 ID 相关的所有红点数
 */
- (NSUInteger)badgeCountWithWidgetId:(NSString *)widgetId;
```

四、 红点数据注入接口

``` C
/**
 * 全量方式：覆盖服务端返回的红点信息，并缓存
 *
 * @param badgeList MPBadgeInfo 数组
 *
 * @return 无
 */
- (void)updateRemoteBadgeInfo:(NSArray *)badgeList;

/**
 * 增量方式：插入（红点）服务端返回的红点信息，并缓存
 *
 * @param badgeList MPBadgeInfo 数组
 *
 * @return 无
 */
- (void)insertRemoteBadgeInfo:(NSArray *)badgeList;

/**
 * 增量方式：插入本地红点信息
 * 注意： 1、不做本地缓存； 2、点击不上报服务器；
 *
 * @param badgeList 红点数据，元素是 MPBadgeInfo
 *
 * @return 无
 */
- (void)insertLocalBadgeInfo:(NSArray *)badgeList;
```

### 3.2.2 MPBadgeInfo
红点数据，定义红点样式，父子关系。
``` C
@property(nonatomic, readonly)  NSString *badgeId;  // 红点路径 ID
@property(nonatomic, copy)      NSString *style;    // 控件显示样式；三种："point","new","num"
@property(nonatomic, assign)    NSUInteger temporaryBadgeNumber;   // 临时红点，点击即消失，不上报服务端
@property(nonatomic, assign)    NSUInteger persistentBadgeNumber;  // 持久红点，点击上报服务端，服务端控制消失

/**
 * 创建红点数据
 *
 * @param badgeId 红点路径 ID
 *        注意：若存在父子关系时，用逗号分割，如:"#M_PUBLIC_RD#2013112300002181,#M_TRANS_RD#2088502524001315"
 *
 * @return 红点数据
 */
+ (MPBadgeInfo *)badgeInfoWithBadgeId:(NSString *)badgeId;

/**
 * 获取红点路径上的所有红点控件 ID
 *
 * @param 无
 *
 * @return 返回红点控件 ID
 */
- (NSArray *)allWidgetIds;
```

### 3.2.3 MPAbsBadgeView

红点控件抽象基类

``` C
@property(nonatomic, copy) NSString *widgetId; // 红点控件 ID，在 UITableviewCell 中使用时，要注意复用的问题，每次都要设置该属性
@property(nonatomic, copy) void (^ callback)(); // 提供业务监控红点控件刷新接口
@property(nonatomic, strong) MPWidgetInfo *widgetInfo; // 红点控件信息，业务不用关心
@property(nonatomic, weak) id<MPBadgeViewDelegate> delegate; // 刷新代理，业务不用关心

/**
 * 数字的计算方式
 * 注意： 1、叶子节点不要设置该属性；
 *      2、在 UITableviewCell 中使用时，要注意复用的问题，每次都要设置该属性
 *
 * @"point" 只计算红点个数
 * @"new"   只计算 new 个数
 * @"num"   只计算数字个数
 * nil      默认计算方式，所有的个数
 */
@property(nonatomic, copy) NSString *numberCalculateMode;

/**
 * 更新显示“红点”样式
 *
 * @param badgeValue:  @"."   显示红点
 *                     @"new" 显示 new
 *                     @"数字" 显示数字，大于 99 都显示图片more（...）
 *                     @"惠"  显示“惠”字
 *                     nil    清除当前显示
 *
 * @return 无
 */
- (void)updateBadgeValue:(NSString *)badgeValue;

/**
 * 绘制“红点”样式显示
 *
 * @param style:       @"."   显示红点
 *                     @"new" 显示 new
 *                     @"数字" 显示数字，大于 99 都显示图片more（...）
 *                     @"惠"  显示“惠”字
 *                     nil    清除当前显示
 *
 * @return 无
 */
- (void)drawBadgeStyle:(NSString *)style;
```

### 3.2.4 MPBadgeView

红点控件，MPAbsBadgeView 子类。

### 3.2.5 MPBadgeServiceConfig

红点管理服务配置接口

``` C
/**
 * 点击上报
 *
 * @param widgetIds  上报数据
 * @param completion 上报完成回调
 *
 * @return 无
 */
- (void)ack:(NSArray *)widgetIds completion:(void (^)(BOOL success))completion;

/**
 * 字符串加密
 *
 * @param string    待加密的字符串
 *
 * @return 加密后的字符串
 */
- (NSString *)encryptString:(NSString *)string;
```