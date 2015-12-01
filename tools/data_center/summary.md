@cnName 1 概述
@priority 1

# 1 概述

## 1.1 使用统一存储的目的

* 减少 NSUserDefaults 的使用，不将较大数据和有隐私性数据存储在 NSUserDefaults 里，存取效率相对使用 NSUserDefaults 有大幅提升。

* 减少业务自动维护文件的情况，减少 Documents、Library 目录下的杂乱文件。

* 统一存储分按存储空间划分为：与用户无关的空间，当前用户的存储空间。业务层无需关注用户切换，并且不需要使用 userId 来获取当前用户数据。

* 基于 sqlite，提供 DAO 支持，相比 CoreData 更加灵活。通过配置文件将数据库操作封装起来并与业务隔离，业务层使用接口存取数据，操作数据库表。

* 底层提供数据加密支持。

* 提供多样化的存储方式，满足不同需求，并提供高效的内存缓存。

## 1.2 统一存储公开类说明

|类名|功能|
|-|-|-|
| APDataCenter |单例类，统一存储入口类|
| APSharedPreferences |对应一个数据库文件，提供 Key-Value 存储接口，同时容纳 DAO 建表。|
| APDataCrypt |对称加密结构体|
| APLRUDiskCache |支持 LRU 淘汰规则的磁盘缓存|
| APLRUMemoryCache |支持 LRU 淘汰规则的内存缓存，线程安全。|
| APObjectArrayService |基于 DAO，可以分业务对支持 NSCoding 的对象提供持久化，支持加密、容量限制与内存缓存。|
| APAsyncFileArrayService |基于 DAO，对二进制数据提供持久化，支持加密、容量限制与内存缓存。|
| APCustomStorage |自定义存储空间，同时在这个空间内提供完整的用户管理，Key-Value、DAO 存储功能。|
| APDAOProtocol |接口描述，为 DAO 对象支持的接口。|