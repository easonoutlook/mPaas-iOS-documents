@cnName 2.5.2 APAsyncFileArrayService
@priority 2

### 2.5.2 APAsyncFileArrayService

[TOC]

#### Introduction

APAsyncFileArrayService offers file array storage using the database.  You can use this service for storing a large number of similar files (such as voice chatting files).  APCustomStorage is used to create this service.  The business should create the APCustomStorage first, specify your working directory, create an APAsyncFileArrayService with APCustomStorage, and then use the service to write the file array in the database file in your own directory. 

#### Interface introduction

**For the asynchronous interface with incoming completion argument, the completion argument will definitely be called back in the main thread, and its value can be nil. **

** - (void)writeFile:(NSData*)data name:(NSString*)name completion:(void(\^)(BOOL result))completion;**
```
This interface writes a file named "name". When the asynchronous write is complete, the completion argument will be called back.  In result, it returns whether the call is successful. 
```

** - (void)readFile:(NSString*)name completion:(void(\^)(NSData* data))completion;**
```
This interface reads the file named "name" asynchronously. When the operation is complete, the file data will be returned in the completion call.
```

** - (NSData*)readFileSync:(NSString*)name;**
```
This interface reads the file synchronously. 
```

** - (void)readFilesLike:(NSString*)pattern completion:(void(\^)(NSDictionary* result))completion;**
```
This interface uses the like function of SQLite to read batch data. The result returns the dictionary of the file name and file data. It is an asynchronous interface.
```

** - (void)renameFile:(NSString*)name newName:(NSString*)newName completion:(void(\^)(BOOL result))completion;**
```
This interface renames the file asynchronously. 
```

** - (void)removeFile:(NSString*)name;**
```
This interface deletes the file asynchronously. 
```

** - (void)removeFilesLike:(NSString*)pattern;**
```
This interface deletes data in batch using the LIKE function of SQLite. 
```

** - (void)removeAllFiles;**
```
This interface deletes all the files asynchronously. 
```

** - (BOOL)fileExists:(NSString*)name;**
```
This interface checks whether a file exists. It is a synchronous interface. 
```

** - (NSArray*)allFileNames;**
```
This interface gets names of all the files. It is a synchronous interface. 
```

** - (NSInteger)fileCount;**
```
This interface gets the file count. It is a synchronous interface. 
```

