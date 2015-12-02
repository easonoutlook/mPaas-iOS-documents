@cnName 2.3.4 DAO 代理
@priority 4

### 2.3.4 DAO 代理

每个生成的 DAO 对象代理对象都支持 APDAOProtocol。

比如
```C
@protocol MyDAOOperations <APDAOProtocol>
- (APDAOResult*)insertMessage:(MyMessageModel*)model;
@end
```
具体方法见代码的函数注释，如果有特殊需求，可以提给我，添加方法。

```C
#import <Foundation/Foundation.h>
#import <sqlite3.h>
#import "APDataCrypt.h"
#import "APDAOResult.h"
#import "APDAOTransaction.h"

@protocol APDAOProtocol;

typedef NS_ENUM (NSUInteger, APDAOProxyEventType)
{
    APDAOProxyEventShouldUpgrade = 0,   // 即将升级
    APDAOProxyEventUpgradeFailed,       // 表升级失败
    APDAOProxyEventTableCreated,        // 表被创建
    APDAOProxyEventTableDeleted,        // 表被删除
};

typedef void(^ APDAOProxyEventHandler)(id<APDAOProtocol> proxy, APDAOProxyEventType eventType, NSDictionary* arguments);

/**
 *  这个 Protocol 定义的方法每个 DAO 代理对象都支持，使用时使用 id<APDAOProtocol>对 DAO 对象进行转换。
 */
@protocol APDAOProtocol <NSObject>

/**
 *  配置文件里 module 可以设置表名，如果想实现配置文件作为一个模板，操作不同的表，可以生成 DAO 对象后手工向 DAO 对象设计表名。
 *  比如要对与每个 id 的对话消息进行分表的情况。
 */
@property (atomic, strong) NSString* tableName;

/**
 *  返回这个 proxy 操作的表所在数据库文件的路径
 */
@property (atomic, strong, readonly) NSString* databasePath;

/**
 *  获取这个 proxy 操作的数据库文件的句柄
 */
@property (atomic, assign, readonly) sqlite3* sqliteHandle;

/**
 *  注册全局变量参数，这些参数配置文件里的所有方法都可以使用，在配置文件里使用#{name}和@{name}来访问。
 */
@property (atomic, strong) NSDictionary* globalArguments;

/**
 *  这个 proxy 的事件回调，业务自行设置。回调线程不确定。
    注意循环引用的问题，业务对象持有 proxy，这个 handler 方法里不要访问业务对象或 proxy，proxy 可以在回调的第一个参数里拿到。
 */
@property (atomic, copy) APDAOProxyEventHandler proxyEventHandler;

/**
 *  设置加密器，用于加密表里标记为需要加密的列的数据。向表里写数据时，碰到这个列，会调用进行加密。
 *
 *  @param crypt 加密结构体，会被拷贝一份。如果传入的 crypt 是外部创建的，需要外部进行 free。如果是 APDefaultEncrypt()，不需要进行释放。
 */
@property (atomic, assign) APDataCrypt* encryptMethod;

/**
 *  设置解密器，用于解密表里标记为需要加密的列的数据。从表里读数据时，碰到这个列，会调用进行解密。
 *
 *  @param crypt 解密结构体，会被拷贝一份。如果传入的 crypt 是外部创建的，需要外部进行 free。如果是 APDefaultDecrypt()，不需要进行释放。
 */
@property (atomic, assign) APDataCrypt* decryptMethod;

/**
 *  返回 sqlite 的最后一条 rowId
 *
 *  @return sqlite3_last_insert_rowid()
 */
- (long long)lastInsertRowId;

/**
 *  获取配置文件定义的所有方法列表
 */
- (NSArray*)allMethodsList;

/**
 *  删除配置文件里定义的表，可以用于特殊情况下的数据还原。删除表后，DAO 对象仍可以正常使用，再次调用其它方法，会重新创建表。
 */
- (APDAOResult*)deleteTable;

/**
 *  删除符合某个正则规则的所有表，请确保只删除本 Proxy 操作的表，否则可能发生异常
 *
 *  @param pattern    正则表达式
 *  @param autovacuum 删除完成是否自动调用 vacuum 清理数据库空间
 *  @param progress   进度回调，可传 nil，回调不保证主线程。为百分之后的结果
 *
 *  @return 操作是否成功
 */
- (APDAOResult*)deleteTablesWithRegex:(NSString*)pattern autovacuum:(BOOL)autovacuum progress:(void(^)(float progress))progress;

/**
 *  调用自己的数据库链接执行 vacuum 操作
 */
- (void)vacuumDatabase;

/**
 *  DAO 对象可以自己把操作放在事务里提升速度，实际调用的是该 DAO 对象操作的数据库文件 APSharedPreferences 的 daoTransaction 方法。
 */
- (APDAOResult*)daoTransaction:(APDAOTransaction)transaction;

/**
 *  创建一个数据库副连接，为接下来可能发生的并发select 操作加速使用。可以调用多次，创建多个数据库连接待用。
 *  这些创建的链接会自动关闭，业务层无须处理。
 
 *  @param autoclose 在空闲状态多少秒后自动关闭，0 表示使用系统值
 */
- (void)prepareParallelConnection:(NSTimeInterval)autoclose;

@end
```