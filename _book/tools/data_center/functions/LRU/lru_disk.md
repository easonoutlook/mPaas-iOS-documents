@cnName 2.4.2 磁盘缓存
@priority 2

### 2.4.2 APLRUDiskCache

[TOC]

#### 简介

APLRUDiskCache 提供持久化到数据库的 LRU 淘汰算法缓存，缓存支持 NSCoding 的对象。使用数据库相比文件会更容易维护，也使磁盘更整洁。

#### 接口介绍

** - (id)initWithName:(NSString*)name capacity:(NSInteger)capacity userDependent:(BOOL)userDependent crypted:(BOOL)crypted;**
```
创建一个持久化的 LRU 缓存，存入的对象需要支持 NSCoding 协议。
 *  @param name          缓存的名字，用做数据库的表名
 *  @param capacity      容量，实际容量会比这个大一些，解决缓存满时添加数据的性能问题
 *  @param userDependent 是否与用户相关，如果与用户相关，当 APDataCenter.currentUserId 为空时缓存无法操作；
        当切换用户后，缓存会自动指向当前用户的表，业务无须关心这个事件。
 *  @param crypted       数据是否加密
 *  @return 缓存实例，业务需要持有
 ```

** - (void)setObject:(id)object forKey:(NSString*)key;**
```
缓存一个对象，expire 默认为 0，也就是永不过期
```

** - (void)setObject:(id)object forKey:(NSString*)key expire:(NSTimeInterval)expire;**
```
缓存一个对象，并指定过期的时间戳
 *  @param object 对象，如果为 nil 会删除指定 key 的对象
 *  @param key    key
 *  @param expire 过期时间戳，指定一个相对于 1970 的绝对时间戳。可以使用[date timeIntervalSince1970]。
```

** - (id)objectForKey:(NSString*)key;**
```
取对象，如果对象读取出来时，指定的 expire 时间戳已经达到，会返回 nil，并删除对象。如果调用 objectForKey 前，有其它 setObject 操作写数据库未完成，会等待。
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
批量添加数据
```

** - (void)removeObjectsWithSqlLike:(NSString*)like;**
```
批量删除数据，数据的 key 使用 sqlite 的 like 语句匹配
```

** - (void)removeObjectsWithKeys:(NSSet*)keys;**
```
批量删除所有 keys 指定的数据
```