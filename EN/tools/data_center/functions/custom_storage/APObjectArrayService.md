@cnName 2.5.3 APObjectArrayService
@priority 3

### 2.5.3 APObjectArrayService

[TOC]

#### Introduction

APObjectArrayService is implemented based on APAsyncFileArrayService and stores Objective-C objects supporting the NSCoding protocol. Unlike APAsyncFileArrayService, this service offers the memroy cache feature.  It should be created using APCustomStorage.  The business should create the APCustomStorage first, specify your working directory, create an APObjectArrayService with APCustomStorage, and then use the service to write the object array in the database file in your own directory.

#### Interface introduction

** - (void)setObject:(id)object forKey:(NSString*)key; // Write IO is asynchronous. **
```
This interface sets the object. 
```

** - (id)objectForKey:(NSString*)key; // If the object does not exist in the cache, read the database synchronously.**
```
This interface fetches the object.
```

** - (void)objectForKey:(NSString*)key completion:(void(^)(id object))completion;**
```
This interface reads the object asynchronously. After the read is successful, the object is returned in the main thread in the completion call. 
```

** - (void)objectsForKeyLike:(NSString*)pattern completion:(void(^)(NSDictionary* result))completion;**
```
This interface uses the LIKE function of SQLite to read batch data. The data is the dictionary of the object name and object.  It does not check the cache. 
```

** - (void)removeObjectForKey:(NSString*)key;**
```
This interface deletes data. 
```

** - (void)removeObjectsForKeyLike:(NSString*)pattern;**
```
This interface deletes data in batch using the LIKE function of SQLite.
```

** - (void)removeAllObjects;**
```
This interface deletes all the data.
```

** - (BOOL)objectExistsForKey:(NSString*)key;**
```
This interface judges whether a key-value data exists. 
```

** - (NSArray*)allKeys;**
```
This interface gets all the keys in the cache. 
```

** - (NSInteger)objectCount;**
```
This interface gets the count of cached objects. 
```
