@cnName 2.5.2 文件阵列服务
@priority 2

### 2.5.2 APAsyncFileArrayService

[TOC]

#### 简介

APAsyncFileArrayService 这个服务是利用数据库提供文件阵列存储功能。当有大量类似文件（比如聊天里的语音）需要存储时，可以使用这个服务。这个服务需要使用 APCustomStorage 创建。业务先创建 APCustomStorage，指定自己的工作目录，然后再用 APCustomStorage 创建一个 APAsyncFileArrayService，再使用这个 service 就可以在自己的目录内的数据库文件里写入文件阵列了。

#### 接口介绍

**对于传入了 completion 的异步接口，completion 一定会在主线程回调，可为 nil。**

** - (void)writeFile:(NSData*)data name:(NSString*)name completion:(void(\^)(BOOL result))completion;**
```
写入文件，文件名是 name，当异步写入完成时，会回调 completion。result 返回是否成功。
```

** - (void)readFile:(NSString*)name completion:(void(\^)(NSData* data))completion;**
```
异步读取文件，文件名是 name，完成时会将文件数据在 completion 返回。
```

** - (NSData*)readFileSync:(NSString*)name;**
```
同步读文件
```

** - (void)readFilesLike:(NSString*)pattern completion:(void(\^)(NSDictionary* result))completion;**
```
使用 sqlite 的 like 功能读取批量数据，result 为文件名与文件数据的字典，异步接口。
```

** - (void)renameFile:(NSString*)name newName:(NSString*)newName completion:(void(\^)(BOOL result))completion;**
```
异步重命名文件
```

** - (void)removeFile:(NSString*)name;**
```
异步删除文件
```

** - (void)removeFilesLike:(NSString*)pattern;**
```
使用 sqlite 的 like 功能批量删除数据
```

** - (void)removeAllFiles;**
```
异步删除所有文件
```

** - (BOOL)fileExists:(NSString*)name;**
```
同步接口，文件是否存在
```

** - (NSArray*)allFileNames;**
```
同步接口，取所有文件名
```

** - (NSInteger)fileCount;**
```
同步接口，文件数目
```