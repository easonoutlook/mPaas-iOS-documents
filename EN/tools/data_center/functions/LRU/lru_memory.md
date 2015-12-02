@cnName 2.4.1 APLRUMemoryCache
@priority 1

## 1.2.4.1 APLRUMemoryCache

[TOC]

#### Introduction

APLRUMemoryCache offers to the memory a cache service in the LRU elimination algorithm for caching ID objects.  APLRUMemoryCache is thread-safe and the LRU algorithm is achieved based on the chain table structure, featuring high efficiency. 

#### Interface introduction

** - @property (nonatomic, assign) BOOL handleMemoryWarning; // default NO **
```
This interface sets whether to process the memory warnings in the system. Its value is NO by default.  If it is set to YES, the cache will be cleared when there is a memory warning. 
```

** - (id)initWithCapacity:(NSInteger)capacity;**
```
This interface specifies the capacity at initiation. 
```

** - (void)setObject:(id)object forKey:(NSString*)key;**
```
This interface stores the object into the cache. If the object is nil, the object will be deleted. 
```

** - (void)setObject:(id)object forKey:(NSString*)key expire:(NSTimeInterval)expire;**
```
This interface stores the object into the cache and specifies an expiration timestamp. 
```

** - (id)objectForKey:(NSString*)key;**
```
This interface fetches the object. 
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
This interface adds data in batch. It is not able to set the expire time for each individual object, and objects will never expire by default. 
```

** - (void)removeObjectsWithRegex:(NSString*)regex;**
```
This interface deletes data in batch. The data key matches regex. 
```

** - (void)removeObjectsWithPrefix:(NSString*)prefix;**
```
This interface deletes all the data with a certain prefix in batch. 
```

** - (void)removeObjectsWithSuffix:(NSString*)suffix;**
```
This interface deletes all the data with a certain suffix in batch.
```

** - (void)removeObjectsWithKeys:(NSSet*)keys;**
```
This interface deletes all the data specified by the keys. 
```

** - (NSArray*)peekObjects:(NSInteger)count fromHead:(BOOL)fromHead;**
```
This interface reads the cached data into an array, but no LRU cache policy processing will be done.  When the fromHead value is YES, the traversal starts from the head, otherwise, from the end. 
```

** - (BOOL)objectExistsForKey:(NSString*)key;**
```
This interface quickly judges whether an object for a certain key exists. It will not affect LRU. 
```

** - (void)resetCapacity:(NSInteger)capacity;**
```
This interface updates the capacity. If the new capacity is smaller than the old one, part of the cache will be deleted. 
```
