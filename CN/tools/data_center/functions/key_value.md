@cnName 2.2 Key-Value 存储
@priority 2

## 2.2 Key-Value 存储

[TOC]

### 2.2.1 简介

客户端许多场景使用 Key-Value 存储就能很好的满足需求，通常我们会使用 NSUserDefaults，但 NSUserDefaults 不支持加密，持久化速度慢。

统一存储Key-Value 存储提供接口存储： NSInteger，long long（ 64 位上与 NSInteger 相同），BOOL，double，NSString 等 PList 对象，支持 NSCoding 的对象，可通过反射转换成 JSON 表达的 Objective-C 对象，极大简化客户端持久化对象的复杂度。

关于Key-Value 存储中大部分接口说明，请开发者参考 APSharedPreferences.h 头文件方法描述。

### 2.2.2 存储基本类型

统一存储提供下列接口存储基本类型。
```
- (NSInteger)integerForKey:(NSString*)key business:(NSString*)business;
- (NSInteger)integerForKey:(NSString*)key business:(NSString*)business defaultValue:(NSInteger)defaultValue; // 当数据不存在时默认值
- (void)setInteger:(NSInteger)value forKey:(NSString*)key business:(NSString*)business;

- (long long)longLongForKey:(NSString*)key business:(NSString*)business;
- (long long)longLongForKey:(NSString*)key business:(NSString*)business defaultValue:(long long)defaultValue; // 当数据不存在时默认值
- (void)setLongLong:(long long)value forKey:(NSString*)key business:(NSString*)business;

- (BOOL)boolForKey:(NSString*)key business:(NSString*)business;
- (BOOL)boolForKey:(NSString*)key business:(NSString*)business defaultValue:(BOOL)defaultValue; // 当数据不存在时默认值
- (void)setBool:(BOOL)value forKey:(NSString*)key business:(NSString*)business;

- (double)doubleForKey:(NSString*)key business:(NSString*)business;
- (double)doubleForKey:(NSString*)key business:(NSString*)business defaultValue:(double)defaultValue; // 当数据不存在时默认值
- (void)setDouble:(double)value forKey:(NSString*)key business:(NSString*)business;
```

其中`defaultValue`参数为数据不存在时返回的默认值。


### 2.2.3 存储Objective-C 对象

#### 接口说明

统一存储提供下列接口存储Objective-C 对象。
```
- (NSString*)stringForKey:(NSString*)key business:(NSString*)business;
- (NSString*)stringForKey:(NSString*)key business:(NSString*)business extension:(APDataCrypt*)extension;
- (void)setString:(NSString*)string forKey:(NSString*)key business:(NSString*)business;
- (void)setString:(NSString*)string forKey:(NSString*)key business:(NSString*)business extension:(APDataCrypt*)extension;

- (id)objectForKey:(NSString*)key business:(NSString*)business;
- (id)objectForKey:(NSString*)key business:(NSString*)business extension:(APDataCrypt*)extension;

- (void)setObject:(id)object forKey:(NSString*)key business:(NSString*)business;
- (void)setObject:(id)object forKey:(NSString*)key business:(NSString*)business extension:(APDataCrypt*)extension;
- (BOOL)setObject:(id)object forKey:(NSString*)key business:(NSString*)business extension:(APDataCrypt*)extension options:(APDataOptions)options;

- (void)archiveObject:(id)object forKey:(NSString*)key business:(NSString*)business;
- (void)archiveObject:(id)object forKey:(NSString*)key business:(NSString*)business extension:(APDataCrypt*)extension;
- (BOOL)archiveObject:(id)object forKey:(NSString*)key business:(NSString*)business extension:(APDataCrypt*)extension options:(APDataOptions)options;

- (void)saveJsonObject:(id)object forKey:(NSString*)key business:(NSString*)business;
- (void)saveJsonObject:(id)object forKey:(NSString*)key business:(NSString*)business extension:(APDataCrypt*)extension;
- (BOOL)saveJsonObject:(id)object forKey:(NSString*)key business:(NSString*)business extension:(APDataCrypt*)extension options:(APDataOptions)options;
```

** setString & stringForKey **

保存 NSString 时，推荐使用 setString，stringForKey 接口，名称上更有解释性。

如果数据未加密，使用这个接口存储的字符串，可以通过 sqlite db 查看器看到，更直观。使用 setObject 保存的字符串会首先通过 Property List 转成 NSData 再保存到数据库里。

** setObject **

保存 Property List 对象时，建议使用 setObject，这样效率最高。

`Property List 对象`： NSNumber、NSString、NSData、NSDate、NSArray、NSDictionary，并且 NSArray 和 NSDictionary 里的子对象也只能是 PList 对象。

使用 setObject 保存 Property List 后，使用 objectForKey 取到的对象是 Mutable 的。下面代码里拿到的 savedArray 是 NSMutableArray。
```
NSArray* array = [[NSArray alloc] initWithObjects:@"str", nil];
[APCommonPreferences setObject:array forKey:@"array" business:@"biz"];

NSArray* savedArray = [APCommonPreferences objectForKey:@"array" business:@"biz"];
```

** archiveObject **

对于支持 NSCoding 协议的 Objective-C 对象，统一存储调用系统的 NSKeyedArchiver 将对象转成 NSData 并进行持久化。

Property List 对象也可以使用这个接口，但效率比较低，不推荐。

** saveJsonObject **

当一个 Objective-C 对象既不是 Property List 对象，也不支持 NSCoding 协议时，可以使用这个方法进行持久化。

这个方法通过运行时动态反射，将 Objective-C 对象映射成 JSON 字符串。但不是所有 Objective-C 对象都可以使用这个方法保存，比如含有 C 结构体指针的属性，互相引用的 Objective-C 对象，对象属性里有字典或数组。

** objectForKey **

统一存储保存 Objective-C 对象数据时，会同时记录它的归档方式，取对象统一使用 objectForKey。注意：使用 setString 保存的字符串需要使用 stringForKey 获取。

#### 数据加密

** 使用默认加密 **

带 extension 的接口支持加密，传入 APDataCrypt 结构体。

`APDefaultEncrypt`为默认加密方法，为 AES 对称加密。

`APDefaultDecrypt`为默认解密方法，与`APDefaultEncrypt`使用相同密钥。

通常情况下使用统一存储提供的默认加密即可，如下：
```
[APUserPreferences setObject:aObject forKey:@"key" business:@"biz" extension:APDefaultEncrypt()];

id obj = [APUserPreferences objectForKey:@"key" business:@"biz" extension:APDefaultDecrypt()];
// or
id obj = [APUserPreferences objectForKey:@"key" business:@"biz"];
```
因为使用的是默认加密，所以取数据的接口可以省略 extension 参数。

** 使用自定义加密方法 **

如果业务有更高的加密安全要求，可以自己实现 APDataCrypt 结构体，并指定加密、解密的函数指针。但请保证加密与解密方法对应，这样才能正确保存、还原数据。
 
** 基础类型加密 **

如果想加密存储BOOL，NSInteger，double，long long，可以把它们转成字符串，或放到 NSNumber 里，再调用 setString、setObject 接口即可。

#### 指定 options

```
typedef NS_OPTIONS (unsigned int, APDataOptions)
{
    // 这两个选项不要在接口中使用，是标识数据的加密属性的，请使用extension来传递加密方法
    APDataOptionDefaultEncrypted    = 1 << 0,       // 这个选项不要传，传了也没效果，统一存储会使用接口里的extension来做加密的判断，而不是options
    APDataOptionCustomEncrypted     = 1 << 1,       // 这个选项不要传，传了也没效果，统一存储会使用接口里的extension来做加密的判断，而不是options
    
    // 标识该数据在清理缓存时可被清除，这里用unsinged int强转1，为因为某些编译选项下，右边的1<<31不能按照unsigned int 来计算，导致赋值失败
    APDataOptionPurgeable           = (unsigned int)1 << 31,
};
```

可以在 setObject/archiveObject/saveJsonObject 三个方法中指定 options。

___APDataOptionPurgeable___ 该数据是可以在数据清理时自动清除。[2.6 数据清理](./clean.md)