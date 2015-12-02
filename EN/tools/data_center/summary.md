@cnName 1 Overview
@priority 1

# 1 Overview

## 1.1 Objective of using Data Center

* To reduce the usage of NSUserDefaults. Data of bigger sizes and private data are not stored in NSUserDefaults, which greatly enhances the access efficiency than using the NSUserDefaults. 

* To reduce automatic maintenance of files by the business, and reduce the disorganized files in the Documents or Library directories. 

* By storage space, Data Center can be divided into the user independent space and the storage space of the current user.  The business layer does not need to care about the switches of users, and does not need to access the current user data with userId. 

* Based on SQLite, Data Center supports DAO which is more flexible than CoreData.  By encapsulating the database operations with configuration files and separating databases with the business, the business layer accesses data and operates on the database tables through interfaces. 

* The underlying layer provides data encryption support. 

* It offers versatile storage means to meet diversified demands and provides highly efficient memory cache service. 

## 1.2 Descriptions of public class of Data Center

|Class Name|Function|
|-|-|-|
| APDataCenter |Singleton class and the entry class of Data Center|
| APSharedPreferences |It corresponds to a database file and offers Key-Value storage interfaces. It stores DAO-created tables.  |
| APDataCrypt |The struct of symmetric encryption|
| APLRUDiskCache |Disk cache that supports LRU knock-out rules|
| APLRUMemoryCache |Memory cache that supports LRU knock-out rules, thread-safe.  |
| APObjectArrayService |Based on DAO, the class provides persistence function for NSCoding-supporting objects by different businesses. It supports encryption, capacity limit and memory cache.   |
| APAsyncFileArrayService |Based on DAO, the class provides persistence function for binary data. It supports encryption, capacity limit and memory cache.  |
| APCustomStorage |To customize the storage space in which the complete functions including user management, Key-Value and DAO storage are provided.  |
| APDAOProtocol |Descriptions of interfaces supported by DAO objects.  |

