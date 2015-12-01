@cnName 汉字拼音处理
@priority 4

# 汉字拼音处理

[TOC]

## 1 简介

汉字拼音处理是比较繁琐的工作，移动框架为开发者提供的汉字拼音处理模块可以大大简化开发者在 iOS 应用上的处理难度，具有以下功能特点：

（1）`汉字转拼音` 
* 根据汉字的 Unicode 码直接取出汉字的拼音 

（2）`搜索功能`
*  支持 2 个字段的搜索，其中主字段可以支持拼音，多音字搜索，副字段按字符串匹配搜索。
*  搜索结果会返回主字段匹配数据的索引，匹配的位置，副字段匹配数据的索引，2 个字段的匹配数据不重复。
*  建索引与搜索速度快。

## 2 功能介绍

### 2.1 汉字转拼音

调用`APPinyinSearchManager`的类方法

```
/**
 @brief 获取字符串的拼音串
 @param text: 输入的字符串
 @return 返回拼音串
 */
+ (NSString *)getPinYinWithText:(NSString *)text;
```

### 2.2 搜索功能

#### 2.2.1 组织数据
待搜索的数据模型需要实现搜索数据协议，定义搜索的主字段和副字段
```
@protocol APPinyinSearchDataProtocol <NSObject>
@required
- (NSString *)primarySearchData;    //搜索主字段
@optional
- (NSString *)secondarySearchData;  //搜索副字段
@end
```

#### 2.2.2 创建搜索索引

调用者创建并持有`APPinyinSearchManager`的实例对象。将组织好的数据传入`APPinyinSearchManager`的对象来创建索引，支持下面 2 种方式创建搜索索引：
1. Manager 不会持有传入的数据，只会持有数据在原始 Dict，Array 中的位置，主副字段。
2. 对一个 Manager 调用 buildIndex 方法时会覆盖原来的搜索索引，传空时会重置搜索索引。
3. 可以建立多个 Manager 实例来搜索不同维度的数据。
4. 建立索引是异步的，如果在建立索引的过程中使用搜索，会在索引完成后自动搜索并回调。

```
/**
 @brief 建立搜索索引
 @param dict: 实现协议的数据  (Ex: {A:[contact1,contact2,contact3], B:[contact4,contact5]})
 @param indexChar: 数据 Dictionary 的有序 keys,用于返回数据的优先级 Ex:[A,B]
 */
- (void)buildSearchIndexWithDataDict:(NSDictionary *)dict indexChar:(NSArray *)indexChar;

/**
 @brief 建立搜索索引
 @param array: 数据 Array Ex:[contact1,contact2,contact3]
 */
- (void)buildSearchIndexWithDataArray:(NSArray *)array;
```

#### 2.2.3 搜索

如果调用搜索时正在创建搜索索引，`APPinyinSearchManager`会在索引建立好后顺序搜索并回调。

```
/**
 @brief 搜索数据,优先主字段，主字段拼音匹配，主副字段匹配数据不重复
 @param searchText: 搜索串
 @param owner: 调用的 owner, 一般传入 self，为取消搜索使用
 @param callBack: 搜索结果的回调
 */
- (void)search:(NSString*)searchText owner:(id)owner completionBlock:(APSearchCallback)callback;
```

#### 2.2.4 返回搜索结果

搜索结果通过下面的回调函数 Block 返回

```
/**
 @brief 搜索字符串的回调
 @param searchText: 搜索串
 @param primaryMatchArrayWithPosition: 主字段匹配出来的数据，数组中的对象是 APSearchPosition
 @param secondaryMatchArray: 副字段的匹配出来的数据，数组中的对象是 NSIndexPath
 @param error:预留错误字段
 */
typedef void (^ APPinyinSearchCallback) (NSString * searchText, NSMutableArray * primaryMatchArrayWithPosition, NSMutableArray * secondaryMatchArray ,NSError * error);
```

其中`primaryMatchArrayWithPosition`数组中的数据结构如下

```
@interface APPinyinSearchPosition : NSObject
/**
 @brief 名称匹配开始的位置
 */
@property (nonatomic, assign) int matchStart;
/**
 @brief 名称匹配结束的位置
 */
@property (nonatomic, assign) int matchEnd;
/**
 @brief 是否是全拼匹配
 */
@property (nonatomic, assign) BOOL matchAllInPy;
/**
 @brief 是否是所有 word 匹配
 */
@property (nonatomic, assign) BOOL matchAllInWord;
/**
 @brief 搜索数据对应 IndexPath。
 	如果是字典，对应 section:key 的位置，row 是数据在 value 的位置。
 	如果是数组，对应 section:0, row 是数据在数组中的位置。
 */
@property (nonatomic, retain) NSIndexPath * indexPath;
```

调用代码示范

```
[self.contactSearchManager search:searchText owner:self completionBlock:^(NSString *searchText, NSMutableArray *primaryMatchArrayWithPosition, NSMutableArray *secondaryMatchArray, NSError *error) {
    if (weakSelf.isSearchMode && [searchText isEqualToString:self.searchBar.text]) {
        weakSelf.searchResultArray = [[NSMutableArray alloc] init];
        //找到主字段匹配到的数据
        for (int i = 0; i < [primaryMatchArrayWithPosition count]; i ++) {
            APPinyinSearchPosition * position = [primaryMatchArrayWithPosition objectAtIndex:i];
            NSIndexPath * indexPath = position.indexPath;
            APContactInfo * contact = [weakSelf contactInfoInDataDictWithIndexPath:indexPath];
            //加入结果数组中
            [weakSelf.searchResultArray addObject:contact];
        }
        //记录主字段的位置匹配信息
        weakSelf.searchResultPositionArray = primaryMatchArrayWithPosition;
        //找到副字段的匹配的数据
        for (int i = 0; i < [secondaryMatchArray count]; i++) {
            NSIndexPath * indexPath = [secondaryMatchArray objectAtIndex:i];
            APContactInfo * contact = [self contactInfoInDataDictWithIndexPath:indexPath];
            //加入结果数组中
            [weakSelf.searchResultArray addObject:contact];
        }
        [weakSelf.tableView reloadData];
    }
}];
```

#### 2.2.5 高亮显示匹配

使用`APPinyinSearchPosition`来展示主字段匹配位置

```
if (self.nameSearchPostion) {
    NSUInteger start = self.nameSearchPostion.matchStart;
    NSUInteger end = self.nameSearchPostion.matchEnd;
    if (self.contactInfo.displayName.length > 0) {
        isMatch = YES;
        [self.contactNameLabel setText:self.contactInfo.displayName afterInheritingLabelAttributesAndConfiguringWithBlock:^ NSMutableAttributedString *(NSMutableAttributedString *mutableAttributedString) {
            NSRange redRange = NSMakeRange(start, (end-start+1));
            [mutableAttributedString addAttribute:(NSString *)kCTForegroundColorAttributeName value:(id)APC_Cell_detailLabel_highlightColor.CGColor range:redRange];
            return mutableAttributedString;
        }];
    }
}
```

展示副字段匹配的位置

```
__weak APContactTableViewCell * weakSelf = self;
[self.detailLabel setText:detailString afterInheritingLabelAttributesAndConfiguringWithBlock:^ NSMutableAttributedString *(NSMutableAttributedString *mutableAttributedString) {
    NSRange redRange = [detailString rangeOfString:weakSelf.searchText];
    [mutableAttributedString addAttribute:(NSString *)kCTForegroundColorAttributeName value:(id)APC_Cell_detailLabel_highlightColor.CGColor range:redRange];
    return mutableAttributedString;
}];
```

#### 2.2.6 其他功能

```
/**
 @brief 重置搜索索引
 */
- (void)resetSearchTree;
/**
 @brief 取消响应搜索操作
 @param owner: 搜索方法调用者
 */
- (void)cancelSearchForOwner:(id)owner;
```