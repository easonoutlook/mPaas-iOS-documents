@cnName 2.5.1 APCustomStorage
@priority 1

## 2.5.1 APCustomStorage

[TOC]

#### Introduction

The default storage space for APDataCenter is the '/Documents/Preferences' directory of the application sandbox.

If a business is independent, or has a large volume of data, APCustomStorage can be utilized to create a storage directory of your own.  All the services provided by Data Center apply to this directory, which is similar to the APDataCenter.  E.g.,

```
APCustomStorage* storage = [APCustomStorage storageInDocumentsWithName:@"Contact"];
```
This will create a 'Documents/Contact' directory.  The 'commonPreferences' for storing public data and the 'userPreferences' for storing user-specific data also exist in the directory.  APCustomStorage is similar to APDataCenter, and the business also needs to pay no attention to user switching. 

#### Interface introduction

** + (instancetype)storageInDocumentsWithName:(NSString*)name;**
```
This interface creates a custom storage with its path being /Documents/name. 
```

** - (id)initWithPath:(NSString*)path;**
```
This method is usually not used for creating a custom storage in an arbitrary path specified. The storageInDocumentsWithName can be used instead. The APCustomStorage created with this interface should be held by the business. When multiple APCustomStorages share the same path, errors will occur. 
```

** - (APBusinessPreferences*)commonPreferences;**
```
User independent global storage objects access data using the key-value method.  Different from APDataCenter, for APCustomStorage, the business argument is not required for storing key-value data in the custom storage space of the business. Only the key is required. 
```

** - (APBusinessPreferences*)userPreferences;**
```
The storage object of the current user logged in accesses data using the key-value method.  When it is not in the login status, nil will be returned. Different from APDataCenter, for APCustomStorage, the business argument is not required for storing key-value data in the custom storage space of the business. Only the key is required.
```

** - (id)daoWithPath:(NSString*)filePath userDependent:(BOOL)userDependent;**
```
Refer to the homonymic interface in APDataCenter. 
```

** - (APAsyncFileArrayService*)asyncFileArrayServiceWithName:(NSString*)name userDependent:(BOOL)userDependent capacity:(NSInteger)capacity crypted:(BOOL)crypted;**
```
This interface creates an asynchronous file array management service for storing multiple similar file records.  It will create tables for storage separately in the database file according to the service name list, instead of storing the data in the key-value table.

@param name          Service name, cannot be empty. 
@param userDependent Dependent on the user or not. 
@param capacity      Capacity. When the defined value is exceeded, the earliest data will be cleared automatically.  When the capacity <= 0, it indicates that no capacity limit is set.  The value here is not referring to the byte capacity, but the rows. 
@param crypted       Whether to encrypt the file contents.

@return The service object, which should be held by the business. 
```

** - (APObjectArrayService*)objectArrayServiceWithName:(NSString*)name userDependent:(BOOL)userDependent capacity:(NSInteger)capacity cacheCapacity:(NSInteger)cacheCapacity crypted:(BOOL)crypted;**
```
This interface creates an array management service for storing similar ID objects. It supports memory cache and data encryption. 

@param name          Service name, cannot be empty.
@param userDependent Dependent on the user or not.
@param capacity      Capacity. When the defined value is exceeded, the earliest data will be cleared automatically. When the capacity <= 0, it indicates that no capacity limit is set. The value here is not referring to the byte capacity, but the rows.
@param cacheCapacity The capacity of records in the memory cache. When the cacheCapacity <= 0, it indicates that memory cache is not enabled. 
*  @param crypted       Whether to encrypt the ID objects.

@return The service object, which should be held by the business. 
```

