@cnName 2.4.2 APLRUDiskCache
@priority 2

### 2.4.2 APLRUDiskCache

[TOC]

#### Introduction

APLRUDiskCache offers persistence cache to the database in the LRU elimination algorithm and supports NSCoding objects.  Databases are easier to maintain than files and databases also make the disk tidier. 

#### Interface introduction

** - (id)initWithName:(NSString*)name capacity:(NSInteger)capacity userDependent:(BOOL)userDependent crypted:(BOOL)crypted;**
```
This interface creates a persistence LRU cache. The cached objects should support the NSCoding protocol. 
 *  @param name          Cache name, used as the table name of the database. 
 *  @param capacity      Capacity. The real capacity will be greater than this value, addressing the compromising performance for adding data when the cache is full. 
 *  @param userDependent Dependent on the user or not. If yes, when APDataCenter.currentUserId is empty, the cache will not be operable. 
        After the user is switched, the cache will automatically point to the table of the current user. The business does not need to care about this event. 
 *  @param crypted       Whether to encrypt the data. 
 *  @return The cache instance. The business should hold it. 
 ```

** - (void)setObject:(id)object forKey:(NSString*)key;**
```
This interface caches an object. The expire value is 0 by default, which means that the object will never expire. 
```

** - (void)setObject:(id)object forKey:(NSString*)key expire:(NSTimeInterval)expire;**
```
This interface caches an object and specifies an expiration timestamp. 
 *  @param object Object. If it is nil, the object for the specific key will be deleted. 
 *  @param key    Key
 *  @param expire The expiration timestamp. It specifies an absolute timestamp relative to 1970.  You can use [date timeIntervalSince1970]. 
```

** - (id)objectForKey:(NSString*)key;**
```
This interface fetches the object. If the specified expiration time has been reached when the object is fetched, nil will be returned and the object will be deleted.  If there are uncompleted write operations to the database by another setObject method when the objectForKey is called, the objectForKey will wait for the uncompleted operation to complete. 
```

** - (void)removeObjectForKey:(NSString*)key;**
```
This interface deletes the object.
```

** - (void)removeAllObjects;**
```
This interface deletes all the objects.
```

** - (void)addObjects:(NSDictionary*)objects;**
```
This interface adds data in batch.
```

** - (void)removeObjectsWithSqlLike:(NSString*)like;**
```
This interface deletes data in batch. The data keys are matched with the LIKE statement of SQLite. 
```

** - (void)removeObjectsWithKeys:(NSSet*)keys;**
```
This interface deletes all the data specified by the keys.
```
