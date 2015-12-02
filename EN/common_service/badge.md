@cnName 3 Badge
@priority 3

# 3 Badge

[TOC]

## 3.1 Instroduction

When the client needs to remind the user to pay attention to a certain UI element, it will display a badge on the element. The badges will accumulate with the levels of UI controls.  mPaaS offers a set of solutions to the badge feature on the client. 

The badge component of mPaaS has the following features: 

- The 'BadgeView' widget used by the client on the interface has a unique identifier, i.e., the 'widgetId' property. 
- The badge widget supports rich styles: red points, numbers, updates, etc. The developer can also customize the badge widget. 
- The client uses 'BadgeManager' to manage the display, refreshing and deletion of badge widgets in a unified way. 
- The client registers the badge widget in the 'BadgeManager' at interface initialization, and unregisters the badge widget in the 'BadgeManager' to destroy the badge widget. 
- When the 'BadgeManager' receives display data, it will refresh the corresponding registered badge widget automatically, saving the developer's efforts to write additional codes for controlling the UI elements. 
- The badge components have a parent-child relationship between each other. The number of badges on the parent node is the sum of all the badges on the child nodes. When all the badges on the child nodes disappear, the badges on the parent node will not show, either. 

## 3.2 Definition of interface class

### 3.2.1 MPBadgeManager

MPBadgeManager manages badges, including registration, unregistration, refreshing and data of badge widgets. 

I. Interface of badge manager

``` C
/**
 * Set the configuration object of badge management. 
 *
 * @param config Configuration object. 
 *
 * @return Null
 */
- (void)setBadgeServiceConfig:(id<MPBadgeServiceConfig>)config;

/**
 * After user is logged in, this interface is called to process the display of badges. 
 *
 * @param userId ID of the user currently logged in.
 *
 * @return Null
 */
- (void)refreshAfterLogin:(NSString *)userId;

/**
 * Clear the badge data cached in memory and the display of all the badge widgets on the interface. 
 *
 * @param Null
 *
 * @return Null
 */
- (void)clearAllBadges;
```

II.  Interface used by badge widget

``` C
/**
 * Register badge widgets to the universal badge manager module. 
 *
 * @param badgeView Badge widgets.
 *
 * @return Null
 */
- (void)registerBadgeView:(MPAbsBadgeView *)badgeView;

/**
 * Unregister the badge widget in the universal badge manager module. 
 *
 * @param badgeView Badge widgets.
 *
 * @return Null
 */
- (void)unregisterBadgeView:(MPAbsBadgeView *)badgeView;

/**
 * Clear the badge if it is a leaf badge. Otherwise, leave it as is.  (No need to care about the badge type.)
 *
 * @param badgeView Badge widgets.
 *
 * @return Null
 */
- (void)tapBadgeView:(MPAbsBadgeView *)badgeView;

/**
 * Click on the badge to trigger the display settings related to the badge (No need to care about the badge type). 
 *
 * @param widgetId Badge widget ID. 
 *
 * @return Null
 */
- (void)tapBadgeViewWithWidgetId:(NSString *)widgetId;
```

III. Interface for badge information acquisition

``` C
/**
 * Acquire the badge information according to the specified badge path ID. 
 *
 * @param badgeId Badge path ID, such as: "#M_PUBLIC_RD#2013112300002181,#M_TRANS_RD#2088502524001315". 
 *
 * @return Return badge information for successful acquisition. Otherwise, return nil.
 */
- (MPBadgeInfo *)badgeInfoWithBadgeId:(NSString *)badgeId;

/**
 * Get the total badge count according to the specified business ID. 
 *
 * @param bizId Business ID.
 *
 * @return The total badge count related to the business. 
 */
- (NSUInteger)badgeCountWithBizId:(NSString *)bizId;

/**
 * Get the total badge count according to the specified badge widget ID.
 *
 * @param widgetId Badge widget ID.
 *
 * @return The total badge count related to the specified widget ID.
 */
- (NSUInteger)badgeCountWithWidgetId:(NSString *)widgetId;
```

IV. Interface for badge data injection

``` C
/**
 * Full mode: overwrite the badge information returned from the server and cache the data. 
 *
 * @param badgeList MPBadgeInfo Array
 *
 * @return Null
 */
- (void)updateRemoteBadgeInfo:(NSArray *)badgeList;

/**
 * Incremental mode: insert the badge information returned from the server and cache the data.
 *
 * @param badgeList MPBadgeInfo Array
 *
 * @return Null
 */
- (void)insertRemoteBadgeInfo:(NSArray *)badgeList;

/**
 * Incremental mode: insert the locale badge information. 
 * Attention:  1. No local cache; 2. Clicks on the badges are not reported to the server. 
 *
 * @param badgeList Badge data. The element is MPBadgeInfo.
 *
 * @return Null
 */
- (void)insertLocalBadgeInfo:(NSArray *)badgeList;
```

### 3.2.2 MPBadgeInfo
The badge data that defines badge styles and parent-child relationships. 
``` C
@property(nonatomic, readonly)  NSString *badgeId;  // Badge path ID
@property(nonatomic, copy)      NSString *style;    // Widget display styles: "point","new" and "num". 
@property(nonatomic, assign)    NSUInteger temporaryBadgeNumber;   // Temporary badges which will disappear once clicked and clicks on temporary badges are not reported to the server. 
@property(nonatomic, assign)    NSUInteger persistentBadgeNumber;  // Persistent badges. Clicks on persistent badges will be reported to the server and the server control will disappear. 

/**
 * Create badge data. 
 *
 * @param badgeId Badge path ID. 
 *        Attention: When a parent-child relationship exists, separate the ID with commas, such as: "#M_PUBLIC_RD#2013112300002181,#M_TRANS_RD#2088502524001315". 
 *
 * @return Badge data. 
 */
+ (MPBadgeInfo *)badgeInfoWithBadgeId:(NSString *)badgeId;

/**
 * Acquire all the badge widget ID on the badge path. 
 *
 * @param Null
 *
 * @return The badge widget ID. 
 */
- (NSArray *)allWidgetIds;
```

### 3.2.3 MPAbsBadgeView

The abstract base class of badge widget. 

``` C
@property(nonatomic, copy) NSString *widgetId; // Badge widget ID. When it is used in UITableviewCell, attention should be paid to the reuse. This property should be configured every time. 
@property(nonatomic, copy) void (^ callback)(); // Provides the business with the refreshing interface for monitoring badge widgets. 
@property(nonatomic, strong) MPWidgetInfo *widgetInfo; // Badge widget information. The business does not need to care about it. 
@property(nonatomic, weak) id<MPBadgeViewDelegate> delegate; // Refreshing delegate. The business does not need to care about it. 

/**
 * Calculation of numbers. 
 * Attention:  1. This property is not required for leaf nodes. 
 *      2. When the badge widget ID is used in UITableviewCell, attention should be paid to the reuse. This property should be configured every time.
 *
 * @"point" Calculate the "point" count only. 
 * @"new"  Calculate the "new" count only. 
 * @"num"   Calculate the "number" count only. 
 * nil      Default calculation method, including the counts of all. 
 */
@property(nonatomic, copy) NSString *numberCalculateMode;

/**
 * Badge style for updates. 
 *
 * @param badgeValue:   @"."   Display the red point. 
 *                     @"new" Display "new"
 *                     @"数字" Display the number. For numbers greater than 99, display the image "more" (...)
 *                     @"惠"  Display the "惠" character
 *                     nil    Clear current badge display
 *
 * @return Null
 */
- (void)updateBadgeValue:(NSString *)badgeValue;

/**
 * Draw badge styles. 
 *
 * @param style:        @"."   Display the red point.
 *                     @"new" Display "new"
 *                     @"数字" Display the number. For numbers greater than 99, display the image "more" (...)
 *                     @"惠"  Display the "惠" character
 *                     nil    Clear current badge display
 *
 * @return Null
 */
- (void)drawBadgeStyle:(NSString *)style;
```

### 3.2.4 MPBadgeView

A badge widget and a subclass of MPAbsBadgeView. 

### 3.2.5 MPBadgeServiceConfig

The interface for badge manager configuration. 

``` C
/**
 * Report clicks. 
 *
 * @param widgetIds  Report data. 
 * @param completion Callback after reporting completion. 
 *
 * @return Null
 */
- (void)ack:(NSArray *)widgetIds completion:(void (^)(BOOL success))completion;

/**
 * Encrypt character strings. 
 *
 * @param string    Character strings to be encrypted.
 *
 * @return Encrypted character strings. 
 */
- (NSString *)encryptString:(NSString *)string;
```
