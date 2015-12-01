@cnName 2.5.3 对象阵列服务
@priority 3

### 2.5.3 APObjectArrayService

[TOC]

#### 简介

APObjectArrayService 基于 APAsyncFileArrayService 实现，存储支持 NSCoding 协议的 Objective-C 对象，相比 APAsyncFileArrayService，这个服务提供了内存缓存功能。这个服务同样需要使用 APCustomStorage 创建。业务先创建 APCustomStorage，指定自己的工作目录，然后再用 APCustomStorage 创建一个 APObjectArrayService，再使用这个 service 就可以在自己的目录内的数据库文件里写入对象阵列了。

#### 接口介绍

** - (void)setObject:(id)object forKey:(NSString*)key; // 写 IO 是异步的**
```
设置对象
```

** - (id)objectForKey:(NSString*)key; // 如果缓存里没有，同步去数据库中读取**
```
取对象
```

** - (void)objectForKey:(NSString*)key completion:(void(^)(id object))completion;**
```
异步读取，读取成功后在主线程调用 completion 返回。
```

** - (void)objectsForKeyLike:(NSString*)pattern completion:(void(^)(NSDictionary* result))completion;**
```
使用 sqlite 的 like 功能读取批量数据，data 为对象名与对象的字典。不检查缓存。
```

** - (void)removeObjectForKey:(NSString*)key;**
```
删除数据
```

** - (void)removeObjectsForKeyLike:(NSString*)pattern;**
```
使用 sqlite 的 like 功能批量删除数据
```

** - (void)removeAllObjects;**
```
删除所有数据
```

** - (BOOL)objectExistsForKey:(NSString*)key;**
```
判断某键值数据是否存在
```

** - (NSArray*)allKeys;**
```
缓存中所有的 Key
```

** - (NSInteger)objectCount;**
```
缓存对象数目
```

