@cnName 2.1 APDataCenter
@priority 1

## 2.1 APDataCenter

[TOC]

### 2.1.1 Brief Introduction

APDataCenter is the entry class of Data Center. It is singleton and can be called anywhere in the codes. 
```
[APDataCenter defaultDataCenter]
```
Macros can be used. 
```
#define APDefaultDataCenter [APDataCenter defaultDataCenter]
```
It means to initialize APDataCenter. 

### 2.1.2 Interface

#### Macro definition

```
#define APDefaultDataCenter [APDataCenter defaultDataCenter]
#define APCommonPreferences [APDefaultDataCenter commonPreferences]
#define APUserPreferences [APDefaultDataCenter userPreferences]
#define APCurrentVersionStorage [APDefaultDataCenter currentVersionStorage]
```

#### Constant definition

These event notifications usually require no attention from the business-level codes, but Data Center will throw these notifications. 
```
/**
 *  Event notification on that the database file of the previous user is about to be closed. 
 */
extern NSString* const kAPDataCenterWillLastUserResign;

/**
 *  Notification on that the user status has been switched.  It is possible that the user changes to nil. The specific userId can be acquired with the currentUserId function. 
 *  The auxiliary object to this notification is a dictionary. If it is not nil, the @"switched" key value in it returns @YES and it indicates that user switch event did happen. 
 */
extern NSString* const kAPDataCenterDidUserUpdated;

/**
 *  The user didn't switch, and APDataCenter receives the sign-in event again.  This notification will be thrown. 
 */
extern NSString* const kAPDataCenterDidUserRenew;
```

### 2.1.3 Interface and property

#### void APDataCenterLogSwitch(BOOL on);
```
Enable or disable the console log of Data Center. It is enabled by default. 
```

#### @property (atomic, strong, readonly) NSString* currentUserId;
```
The userId of the user currently logged in. 
```

#### + (NSString*)preferencesRootPath;
```
Get the path where the commonPreferences and userPreferences database folders are stored. 
```

#### - (void)setCurrentUserId:(NSString*)currentUserId;
```
Set the user ID of the user currently logged in. Do not call it in the business-level codes as it will be called by the login module.  Once the user ID is set, userPreferences will point to the database of this user. 
```
#### - (void)reset;
```
Fully reset the Data Center directories. Please use it with caution. 
```

#### - (APSharedPreferences*)commonPreferences;
```
The user independent global storage database. 
```

#### - (APSharedPreferences*)userPreferences;
```
The storage databse of the user currently logged in.  When it is not in the login status, nil will be returned. 
```

#### - (APSharedPreferences*)preferencesForUser:(NSString*)userId;
```
Return the storage object of the specified user ID. The business layer usually uses userPreferences method for this purpose.  When there is a need for asynchronous storage, this method can be used to acquire the storage database of a specified user to avoid data mess-up. 
```

#### - (APPreferencesAccessor*)accessorForBusiness:(NSString*)business;
```
Generate an data accessor based on the business name. The business layer needs to hold the object by itself.  Once this data accessor is used, the business value will be no longer required for accessing the KV storage. 

APPreferencesAccessor* accessor = [[APDataCenter defaultDataCenter] accessorForBusiness:@"aBiz"];
[[accessor commonPreferences] doubleForKey:@"aKey"];

// Equal to

[[[APDataCenter defaultDataCenter] commonPreferences] doubleForKey:@"aKey" business:@"aBiz"];
```

#### - (APCustomStorage*)currentVersionStorage;
```
Data Center will maintain a database of the current version. When the version is upgraded, the database will be reset. 
```

#### - (id&lt;APDAOProtocol&gt;)daoWithPath:(NSString*)filePath userDependent:(BOOL)userDependent;
```
Generate a DAO access object from a configuration file. 

@param filePath DAO The path of the configuration file. For files in the main bundle, use the method below: 
	NSString* filePath = [[NSBundle mainBundle] pathForResource:@"file" ofType:@"xml"];
@param userDependent Specifies the database operated by the DAO object.  If userDependent=NO, it indicates that it is not user-specific, and DAO will create tables in the database files of commonPreferences.  If userDependent=YES, DAO will create tables in the database files of userPreferences.  After the user is switched, the subsequent DAO operations will automatically switch to the files of the new user, and the business layer does not need to care about the user switch. 

@return The DAO object. The business layer does not need to care about its class name but only needs to enforce the switch using the self-defined id<AProtocol>.  The DAO object returned can be switched with id<APDAOProtocol> as necessary. The method provided by default will be called.  So the self-defined AProtocol should not contain methods defined in APDAOProtocol. 
```

#### - (id&lt;APDAOProtocol&gt;)daoWithPath:(NSString*)filePath databasePath:(NSString*)databasePath;
```
Create a DAO access object maintaining its own independent database files without using APSharedPreferences. 
The DAO object created using daoWithPath:userDependent: interface operates on commonPreferences or userPreferences. 
This interface will create a DAO object operating on the database file specified in the databasePath. If the file does not exist, it will be created. 
Multiple DAO objects can be created pointing to the same databasePath. 

@param filePath     The same as daoWithPath:userDependent: interface. 
@param databasePath The location of the DAO database file. Absolute paths, or relative paths such as 'Documents/XXXX.db' or 'Library/Movie/XXX.db' both work.  

@return DAO object. 
```
