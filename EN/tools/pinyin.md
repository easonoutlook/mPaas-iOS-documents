@cnName Handling of Chinese Pinyin
@priority 4

# Handling of Chinese Pinyin

[TOC]

## 1 Introduction

The processing of Chinese Pinyin is a tedious job. The Chinese Pinyin processing module provided by mPaaS is able to greatly simply the processing on iOS applications. The module has the following features: 

(1) 'Chinese characters to Pinyin' 
* This feature can generate Pinyin accroding to the Unicode of Chinese characters.  

(2) 'Search'
*  This feature supports searching by two fields. Among them, the primary field supports searching of Pinyin and polyphonic characters, and the subsidiary field searches by matched strings. 
*  The search result will return the index and location of matching data for the primary field, and the index of matching data for the subsidiary field. The matching data for the two fields are not duplicated. 
*  This feature is fast for establishing indexes and searching. 

## 2 Function

### 2.1 Chinese characters to Pinyin

Call the method in the 'APPinyinSearchManager' class. 

```
/**
 @brief Acquire the Pinyin string of the character string. 
 @param text:   The input character string. 
 @return The Pinyin string. 
 */
+ (NSString *)getPinYinWithText:(NSString *)text;
```

### 2.2 Search

#### 2.2.1 Organizing data
The data models to be searched should implement the protocol for searching data and define the primary and subsidiary fields for the search. 
```
@protocol APPinyinSearchDataProtocol <NSObject>
@required
- (NSString *)primarySearchData;    //The primary search field.
@optional
- (NSString *)secondarySearchData;  //The subsidiary search field.
@end
```

#### 2.2.2 Creating search index

The caller creates and holds the 'APPinyinSearchManager' instance object.  Create indexes by passing the organized data to the 'APPinyinSearchManager' object. The two methods below are supported for creating the search index: 
1. The APPinyinSearchManager will not hold the incoming data, but the location of the data in the original dictionary and array, and the primary and subsidiary fields. 
2. Calling the buildIndex method for the APPinyinSearchManager will overwrite the old search indexes. When it passes a nil value, the search index will be reset. 
3. You can establish multiple APPinyinSearchManager instances to search for data in different demensions. 
4. The index building is asynchronous. If you want to use the search function while an index is being built, the search operation will be executed and called back automatically after the indexing is complete. 

```
/**
 @brief Building search index. 
 @param dict:  Data for implementing the protocol  (Ex:  {A:[contact1,contact2,contact3], B:[contact4,contact5]})
 @param indexChar:  The ordered keys of data dictionaries. It is used to return the priority of data Ex: [A,B]
 */
- (void)buildSearchIndexWithDataDict:(NSDictionary *)dict indexChar:(NSArray *)indexChar;

/**
 @brief Building search index.
 @param array:  Data array. Ex: [contact1,contact2,contact3]
 */
- (void)buildSearchIndexWithDataArray:(NSArray *)array;
```

#### 2.2.3 Search

If the search is called while an index is being created, 'APPinyinSearchManager' will conduct sequential search and callbacks after the index is created. 

```
/**
 @brief Searched data. The primary field is prioritized and matched by Pinyin. The matching data for the two fields are not duplicated.
 @param searchText:  Searched string. 
 @param owner:  The call owner. Usually "self" is passed. It is used for canceling the search. 
 @param callBack:   Callbacks of the search result. 
 */
- (void)search:(NSString*)searchText owner:(id)owner completionBlock:(APSearchCallback)callback;
```

#### 2.2.4 Returning search result

The search result will be returned through the callback function block below. 

```
/**
 @brief Callbacks of the searched string. 
 @param searchText:  Searched string.
 @param primaryMatchArrayWithPosition:  Matched data for the primary field. The object in the array is APSearchPosition. 
 @param secondaryMatchArray:  Matched data for the subsidiary field. The object in the array is NSIndexPath. 
 @param error: Reservered error field. 
 */
typedef void (^ APPinyinSearchCallback) (NSString * searchText, NSMutableArray * primaryMatchArrayWithPosition, NSMutableArray * secondaryMatchArray ,NSError * error);
```

The data in the 'primaryMatchArrayWithPosition' array is structured as follows: 

```
@interface APPinyinSearchPosition : NSObject
/**
 @brief The starting point of the name match. 
 */
@property (nonatomic, assign) int matchStart;
/**
 @brief The ending point of the name match.
 */
@property (nonatomic, assign) int matchEnd;
/**
 @brief Whether to match all Pinyin. 
 */
@property (nonatomic, assign) BOOL matchAllInPy;
/**
 @brief Whether to match all words. 
 */
@property (nonatomic, assign) BOOL matchAllInWord;
/**
 @brief The matching IndexPath of the searched data. If it is a dictionary, the index path matches the section:key, and the row is the data location in the value. If it is an array, the index path matches the section:0, and the row is the data location in the array. 
 */
@property (nonatomic, retain) NSIndexPath * indexPath;
```

Example calling code:

```
[self.contactSearchManager search:searchText owner:self completionBlock:^(NSString *searchText, NSMutableArray *primaryMatchArrayWithPosition, NSMutableArray *secondaryMatchArray, NSError *error) {
    if (weakSelf.isSearchMode && [searchText isEqualToString:self.searchBar.text]) {
        weakSelf.searchResultArray = [[NSMutableArray alloc] init];
        //Find the matched data for the primary field. 
        for (int i = 0; i < [primaryMatchArrayWithPosition count]; i ++) {
            APPinyinSearchPosition * position = [primaryMatchArrayWithPosition objectAtIndex:i];
            NSIndexPath * indexPath = position.indexPath;
            APContactInfo * contact = [weakSelf contactInfoInDataDictWithIndexPath:indexPath];
            //Add to the result array. 
            [weakSelf.searchResultArray addObject:contact];
        }
        //Record the matched data position for the primary field. 
        weakSelf.searchResultPositionArray = primaryMatchArrayWithPosition;
        //Find the matched data for the subsidiary field. 
        for (int i = 0; i < [secondaryMatchArray count]; i++) {
            NSIndexPath * indexPath = [secondaryMatchArray objectAtIndex:i];
            APContactInfo * contact = [self contactInfoInDataDictWithIndexPath:indexPath];
            //Add to the result array.
            [weakSelf.searchResultArray addObject:contact];
        }
        [weakSelf.tableView reloadData];
    }
}];
```

#### 2.2.5 Highlighting matches

Display the position of matched data for the primary field using 'APPinyinSearchPosition'. 

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

Display the position of matched data for the subsidiary field. 

```
__weak APContactTableViewCell * weakSelf = self;
[self.detailLabel setText:detailString afterInheritingLabelAttributesAndConfiguringWithBlock:^ NSMutableAttributedString *(NSMutableAttributedString *mutableAttributedString) {
    NSRange redRange = [detailString rangeOfString:weakSelf.searchText];
    [mutableAttributedString addAttribute:(NSString *)kCTForegroundColorAttributeName value:(id)APC_Cell_detailLabel_highlightColor.CGColor range:redRange];
    return mutableAttributedString;
}];
```

#### 2.2.6 Others

```
/**
 @brief Reset search index.
 */
- (void)resetSearchTree;
/**
 @brief Cancel response to search operations. 
 @param owner:  Caller of the search method. 
 */
- (void)cancelSearchForOwner:(id)owner;
```