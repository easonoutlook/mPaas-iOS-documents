@cnName 3 常见问题
@priority 3

# 3 常见问题

## 3.1 如何设置统一存储用户态？
接入 mPaaS 的应用会使用自己的账号体系，如果需要使用统一存储来管理用户态数据，请第一时间通知统一存储，让统一存储进行用户数据库的切换，再通知其它业务层。
```C
[[APDataCenter defaultDataCenter] setCurrentUserId:userId];
```
当用户登出时，可以不调用 setCurrentUserId 方法，统一存储会继续打开上一个用户的数据库，不会产生影响。

## 3.2 如何设置自己的默认加密 key？
统一存储提供默认的加密方法，密钥会使用 mPaasInit 方法传入的 appKey 来自动生成，建议接入 mPaaS 的应用使用自己的密钥。

实现 mPaasAppInterface 接口的下列方法，将密钥以 NSData 的方式传给统一存储。
```
#pragma mark 统一存储

/**
 *  如果实现这个方法，要把统一存储默认使用的加密 key 返回，32 字节。这个 key 应用可以使用无线保镖管理，也可以自己加密混淆后写在客户端。
 *  如果不实现也可以，统一存储会使用 mPaas 和 appKey 计算出的一个结果做为加密 key，安全性也足够了。
 *
 *  @return 32 字节的 key，放在 NSData 里
 */
- (NSData*)appDataCenterDefaultCryptKey;
```
建议应用生成自己的 32 字节密钥，并转成 Base64 字符串保存在无线保镖中，在此方法里通过无线保镖的静态接口取出这个字符串，并反解成 NSData。

## 3.3 统一存储是线程安全的吗？
是的，统一存储的数据存储接口都考虑了线程安全性问题，可以在任意线程进行调用。