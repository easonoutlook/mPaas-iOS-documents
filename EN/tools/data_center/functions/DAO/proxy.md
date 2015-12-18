@cnName 2.3.4 DAO Proxy 
@priority 4

### 2.3.4 DAO Proxy

Each proxy object of generated DAO objects supports APDAOProtocol. 

E.g., 
```C
@protocol MyDAOOperations <APDAOProtocol>
- (APDAOResult*)insertMessage:(MyMessageModel*)model;
@end
```
The specific method can be found in function annotations of the codes. With any special method required, submit a request to me. 

```C
#import <Foundation/Foundation.h>
#import <sqlite3.h>
#import "APDataCrypt.h"
#import "APDAOResult.h"
#import "APDAOTransaction.h"

@protocol APDAOProtocol;

typedef NS_ENUM (NSUInteger, APDAOProxyEventType)
{
    APDAOProxyEventShouldUpgrade = 0,   // Will be upgraded very soon.
    APDAOProxyEventUpgradeFailed,       // Table upgrade failed. 
    APDAOProxyEventTableCreated,        // Table is created. 
    APDAOProxyEventTableDeleted,        // Table is deleted. 
};

typedef void(^ APDAOProxyEventHandler)(id<APDAOProtocol> proxy, APDAOProxyEventType eventType, NSDictionary* arguments);

/**
 *  The method defined by this protocol is supported by all the DAP proxy objects. In usage, id<APDAOProtocol> is adopted for converting the DAO object. 
 */
@protocol APDAOProtocol <NSObject>

/**
 *  The module in the configuration file can set table names. If you want to use the configuration file as a template for operations on different tables, you can manually design the table names after the DAO object is generated. 
 *  For example, you want to use different tables for conversation messages with different IDs. 
 */
@property (atomic, strong) NSString* tableName;

/**
 *  Return the path of the database file where the operated table by the proxy is located. 
 */
@property (atomic, strong, readonly) NSString* databasePath;

/**
 *  Acquire the handle of the database file operated table by the proxy.
 */
@property (atomic, assign, readonly) sqlite3* sqliteHandle;

/**
 *  Register the global variable arguments. All the methods in configuration files of the arguments can be used. #{name} and @{name} can be used for accesses in the configuration file. 
 */
@property (atomic, strong) NSDictionary* globalArguments;

/**
 *  The event callback of this proxy is set by the business.  The callback thread is uncertain. 
    Pay attention to the circular reference. The business object holds the proxy. Do not access the business object of proxy in this handler method and the proxy can be acquired in the first argument of the callback. 
 */
@property (atomic, copy) APDAOProxyEventHandler proxyEventHandler;

/**
 *  Set an encryptor for encrypting the data in columns marked for encryption in the table. During data writing to the table, data in this column will be encrypted.
 *
 *  @param crypt The encryption struct which will by copied. If the incoming crypt is created externally, it needs to be freed externally. If it is APDefaultEncrypt(), no free is required.
 */
@property (atomic, assign) APDataCrypt* encryptMethod;

/**
 *  Set a decryptor for decrypting the data in columns marked for encryption in the table. During data reading from the table, data in this column will be decrypted.
 *
 *  @param crypt The decryption struct which will be copied. If the incoming crypt is created externally, it needs to be freed externally. If it is APDefaultDecrypt(), no free is required.
 */
@property (atomic, assign) APDataCrypt* decryptMethod;

/**
 *  Return the rowId of the last row of SQLite. 
 *
 *  @return sqlite3_last_insert_rowid()
 */
- (long long)lastInsertRowId;

/**
 *  Obtain the list of all the methods defined in the configuration file. 
 */
- (NSArray*)allMethodsList;

/**
 *  Delete the table defined in the configuration file. It can be used for data recovery under special circumstances.  After the table is deleted, the DAO object can still work normally. When other methods are called, new tables will be created. 
 */
- (APDAOResult*)deleteTable;

/**
 *  Delete all the tables conforming to a regular expression rule. Make sure you delete tables operated by this proxy only, otherwise exceptions may occur. 
 *
 *  @param pattern    Regular expression
 *  @param autovacuum After the deletion is complete, whether to call “vacuum” to clear the database space. 
 *  @param progress   Progress callback. Nil value is allowed. The callback is not guaranteed to be in the main thread.  It is a percentage value. 
 *
 *  @return Whether the operation is successful.
 */
- (APDAOResult*)deleteTablesWithRegex:(NSString*)pattern autovacuum:(BOOL)autovacuum progress:(void(^)(float progress))progress;

/**
 *  Call the database link of your own to excecute the  “vacuum” operation. 
 */
- (void)vacuumDatabase;

/**
 *  DAO objects can put their operations in transactions to speed up the operation. So in fact, you call the daoTransaction method of the database file APSharedPreferences that this DAO object operates on. 
 */
- (APDAOResult*)daoTransaction:(APDAOTransaction)transaction;

/**
 *  Create a parallel connection for the database, for the purpose of speeding up the possible concurrent SELECT operations that may follow.  This method can be called for multiple times to create several stand-by connections for the database. 
 *  The connections created will be automatically closed and the business layer does not to handle them. 
 
 *  @param autoclose Automatically close the connections if they are idle for a certain number of seconds. The value "0" indicates that the system value will be used. 
 */
- (void)prepareParallelConnection:(NSTimeInterval)autoclose;

@end
```
