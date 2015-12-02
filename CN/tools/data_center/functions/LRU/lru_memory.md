@cnName 2.4.1 内存缓存
@priority 1

### 2.4.1 APLRUMemoryCache

[TOC]

#### 简介

APLRUMemoryCache 提供内存 LRU 淘汰算法的缓存，缓存 id 对象。APLRUMemoryCache 是线程安全的，同时 LRU 算法基于链表实现，效率较高。

#### 接口介绍

** - @property (nonatomic, assign) BOOL handleMemoryWarning; // default NO **
```
设置是否处理系统内存警告，默认为 NO。如果设置为 YES，当有内存警告时会清空缓存。
```

** - (id)initWithCapacity:(NSInteger)capacity;**
```
初始化，指定容量
```

** - (void)setObject:(id)object forKey:(NSString*)key;**
```
将对象存入缓存，如果 object 为 nil，会删除对象
```

** - (void)setObject:(id)object forKey:(NSString*)key expire:(NSTimeInterval)expire;**
```
将对象存入缓存，并指定一个过期时间戳
```

** - (id)objectForKey:(NSString*)key;**
```
取对象
```

** - (void)removeObjectForKey:(NSString*)key;**
```
删除对象
```

** - (void)removeAllObjects;**
```
删除所有对象
```

** - (void)addObjects:(NSDictionary*)objects;**
```
批量添加数据，无法单独设置每个对象的 expire 时间，默认都是永不过期的对象。
```

** - (void)removeObjectsWithRegex:(NSString*)regex;**
```
批量删除数据，数据的 key 匹配 regex 的正则表达式。
```

** - (void)removeObjectsWithPrefix:(NSString*)prefix;**
```
批量删除具有某个前缀的所有数据
```

** - (void)removeObjectsWithSuffix:(NSString*)suffix;**
```
批量删除具有某个后缀的所有数据
```

** - (void)removeObjectsWithKeys:(NSSet*)keys;**
```
批量删除所有 keys 指定的数据
```

** - (NSArray*)peekObjects:(NSInteger)count fromHead:(BOOL)fromHead;**
```
将缓存对象读取到一个数组里，但不做 LRU 缓存策略处理。fromHead 为 YES 时，从头开始遍历，否则对尾开始遍历。
```

** - (BOOL)objectExistsForKey:(NSString*)key;**
```
快速判断某个 key 的对象是否存在，不会影响 LRU。
```

** - (void)resetCapacity:(NSInteger)capacity;**
```
更新容量，如果新容量比原先的小，会删除部分缓存。
```