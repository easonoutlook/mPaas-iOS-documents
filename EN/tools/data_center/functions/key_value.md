@cnName 2.2 Key-Value storage
@priority 2

## 2.2 Key-Value storage

[TOC]

### 2.2.1 Brief Introduction

For many scenarios on the client, the Key-Value storage is sufficient for use. Usually we will use NSUserDefaults, but NSUserDefaults does not support encryption and is slow for persistence data. 

The Key-Value storage of Data Center provides interfaces for storing:   PList objects such as NSInteger, long long (the same as NSInteger in 64-bit environment), BOOL, double and NSString, objects supporting NSCoding, Objective-C objects that can be converted to JSON objects through reflection, greatly reducing the complexity of persistent objects on the client. 

For descriptions on most of the interfaces of Key-Value storage, see the method description in the APSharedPreferences.h header file. 

### 2.2.2 Storing basic type

Data Center provides the following interfaces for storing the primary types of objects:  
```
- (NSInteger)integerForKey:(NSString*)key business:(NSString*)business;
- (NSInteger)integerForKey:(NSString*)key business:(NSString*)business defaultValue:(NSInteger)defaultValue; // Default value when the data does not exist.
- (void)setInteger:(NSInteger)value forKey:(NSString*)key business:(NSString*)business;

- (long long)longLongForKey:(NSString*)key business:(NSString*)business;
- (long long)longLongForKey:(NSString*)key business:(NSString*)business defaultValue:(long long)defaultValue; // Default value when the data does not exist.
- (void)setLongLong:(long long)value forKey:(NSString*)key business:(NSString*)business;

- (BOOL)boolForKey:(NSString*)key business:(NSString*)business;
- (BOOL)boolForKey:(NSString*)key business:(NSString*)business defaultValue:(BOOL)defaultValue; // Default value when the data does not exist.
- (void)setBool:(BOOL)value forKey:(NSString*)key business:(NSString*)business;

- (double)doubleForKey:(NSString*)key business:(NSString*)business;
- (double)doubleForKey:(NSString*)key business:(NSString*)business defaultValue:(double)defaultValue; // Default value when the data does not exist.
- (void)setDouble:(double)value forKey:(NSString*)key business:(NSString*)business;
```

The 'defaultValue' argument is the default value returned when the data does not exist. 


### 2.2.3 Storing Objective-C object

#### 2.2.3.1 Interface description

Data Center provides the following interfaces for storing Objective-C objects:
```
- (NSString*)stringForKey:(NSString*)key business:(NSString*)business;
- (NSString*)stringForKey:(NSString*)key business:(NSString*)business extension:(APDataCrypt*)extension;
- (void)setString:(NSString*)string forKey:(NSString*)key business:(NSString*)business;
- (void)setString:(NSString*)string forKey:(NSString*)key business:(NSString*)business extension:(APDataCrypt*)extension;

- (id)objectForKey:(NSString*)key business:(NSString*)business;
- (id)objectForKey:(NSString*)key business:(NSString*)business extension:(APDataCrypt*)extension;

- (void)setObject:(id)object forKey:(NSString*)key business:(NSString*)business;
- (void)setObject:(id)object forKey:(NSString*)key business:(NSString*)business extension:(APDataCrypt*)extension;

- (void)archiveObject:(id)object forKey:(NSString*)key business:(NSString*)business;
- (void)archiveObject:(id)object forKey:(NSString*)key business:(NSString*)business extension:(APDataCrypt*)extension;

- (void)saveJsonObject:(id)object forKey:(NSString*)key business:(NSString*)business;
- (void)saveJsonObject:(id)object forKey:(NSString*)key business:(NSString*)business extension:(APDataCrypt*)extension;
```

** setString & stringForKey **

The setString and stringForKey interfaces are recommended for storing NSString objects as the names are more interpretative. 

If the data is not encrypted, strings stored with this interface can be viewed in Sqlite DB viewer, being more intuitive.  Strings stored in the setObject method will be first converted to NSData through Property List and then saved to the database. 

** setObject **

The setObject method is recommended for storing Property List objects to achieve the highest efficiency. 

'Property List objects': NSNumber, NSString, NSData, NSDate, NSArray, and NSDictionary. The subobjects in NSArray and NSDictionary must also be PList objects. 

If the setObject method is used for storing Property List objects, the object acquired using the objectForKey method is mutable.  The savedArray acquired in the codes below is of the NSMutableArray class. 
```
NSArray* array = [[NSArray alloc] initWithObjects:@"str", nil];
[APCommonPreferences setObject:array forKey:@"array" business:@"biz"];

NSArray* savedArray = [APCommonPreferences objectForKey:@"array" business:@"biz"];
```

** archiveObject **

For Objective-C objects supporting the NSCoding protocol, Data Center calls the system NSKeyedArchiver and converts the object into an NSData object for persistence storage. 

Property List objects can use this interface, too, but with a low efficiency and thus not recommended. 

** saveJsonObject **

When an Objective-C object is neither a Property List object nor an NSCoding-supporting one, this method can be utilized for persistence storage. 

Through runtime dynamic reflection, this method maps Objective-C objects to JSON strings.  But not all the Objective-C objects can be saved with this method, such as properties containing C structure pointers, Objective-C objects that reference each other, or objects containing dictionaries or arrays in properties. 

** objectForKey **

When Data Center saves the data of Objective-C objects, it will record the archiving methods as well. The objectForKey method is used for acquiring objects.  Attention: Stirngs saved using the setString method should be acquired with the stringForKey method. 

#### 2.2.3.2 Data encryption

** Using default encryption **

Interfaces with extension arguments support encryption, and APDataCrypt struct is passed. 

'APDefaultEncrypt' is the default encryption method using AES symmetric encryption. 

'APDefaultDecrypt' is the default decryption method and share the key with 'APDefaultEncrypt'. 

In usual cases, the default encryption provided by Data Center is used, as follows: 
```
[APUserPreferences setObject:aObject forKey:@"key" business:@"biz" extension:APDefaultEncrypt()];

id obj = [APUserPreferences objectForKey:@"key" business:@"biz" extension:APDefaultDecrypt()];
// or
id obj = [APUserPreferences objectForKey:@"key" business:@"biz"];
```
As the default encryption is adopted, the extension argument can be skipped for the interfaces acquiring data. 

** Using self-defined encryption **

If the business has higher security requirements for encryption, the APDataCrypt struct can be implemented with specified function pointers for encryption and decryption.  But please ensure that the encryption and decryption methods are matched to save and recover data correctly. 
 
** Encrypt primary types **

To encrypt BOOL, NSInteger, double, and long long objects for storage, you can convert them into strings or put them into the NSNumber class, and then call the setString or setObject interface. 


