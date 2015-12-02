@cnName 2.5.1 APCustomStorage
@priority 1

### 2.5.1 APCustomStorage

[TOC]

#### 简介

APDataCenter 对应的默认存储空间为应用沙箱的`/Documents/Preferences`目录。

一个业务比较独立，或数据量比较多，可以使用 APCustomStorage 创建一个自己的存储目录的。在这个目录里也可以使用全部统一存储提供的服务，类似 APDataCenter。比如

```
APCustomStorage* storage = [APCustomStorage storageInDocumentsWithName:@"Contact"];
```
就会创建`Documents/Contact`目录。这个目录里同样有存储公共数据的`commonPreferences`和与用户相关数据的`userPreferences`。APCustomStorage 与 APDataCenter 类似，业务同样无须关注用户切换。

#### 接口介绍

** + (instancetype)storageInDocumentsWithName:(NSString*)name;**
```
创建路径为/Documents/name 的自定义存储
```

** - (id)initWithPath:(NSString*)path;**
```
在任意指定路径创建自定义存储，通常不需要使用这个方法，使用 storageInDocumentsWithName 即可。使用此接口创建的 APCustomStorage，业务需要自己持有，并且当多个 APCustomStorage 的 path 相同时，会出错。
```

** - (APBusinessPreferences*)commonPreferences;**
```
与用户无关的全局存储对象，使用 key-value 方式存取数据。与 APDataCenter 的区别是：在业务的自定义存储空间里，存储key-value 数据时不需要 business 参数，只需要 key 即可。
```

** - (APBusinessPreferences*)userPreferences;**
```
当前登录用户的存储对象，使用 key-value 方式存取数据。不是登录态时，取到的是 nil。与 APDataCenter 的区别是：在业务的自定义存储空间里，存储key-value 数据时不需要 business 参数，只需要 key 即可。
```

** - (id)daoWithPath:(NSString*)filePath userDependent:(BOOL)userDependent;**
```
参考 APDataCenter 的同名接口
```

** - (APAsyncFileArrayService*)asyncFileArrayServiceWithName:(NSString*)name userDependent:(BOOL)userDependent capacity:(NSInteger)capacity crypted:(BOOL)crypted;**
```
创建一个异步的文件阵列管理服务，用于存储互相类似的多条文件记录。会根据服务名单独在数据库文件里建表存储，不放在 key-value 存储的表里。

@param name          服务名，不能为空
@param userDependent 是否与用户相关
@param capacity      容量，超过 capacity 条数据后，会自动清除最早的。capacity <= 0 表示不做数量限制。不是字节容量，是条数。
@param crypted       文件内容是否加密

@return 返回 service 对象，业务自行持有
```

** - (APObjectArrayService*)objectArrayServiceWithName:(NSString*)name userDependent:(BOOL)userDependent capacity:(NSInteger)capacity cacheCapacity:(NSInteger)cacheCapacity crypted:(BOOL)crypted;**
```
创建一个 id 对象存储的阵列管理服务，用于存储类似的 id 对象，支持内存缓存与数据加密。

@param name          服务名，不能为空
@param userDependent 是否与用户相关
@param capacity      容量，超过 capacity 条数据后，会自动清除最早的。capacity <= 0 表示不做数量限制。不是字节容量，是条数。
@param cacheCapacity 内存缓存的条目数容量，cacheCapacity <= 0 时不使用内存缓存。
@param crypted       id 对象是否加密

@return 返回 service 对象，业务需要自行持有
```