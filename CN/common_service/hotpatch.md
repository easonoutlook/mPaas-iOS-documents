@cnName 1 Hotpatch
@priority 1

# 1 Hotpatch

[TOC]

## 1.1 简介

### 1.1.1 Hotpatch 组成部分

苹果商店应用发布有审核过程，无法像安卓平台那样灵活。Hotpatch 提供一种不需要发布新版本，快速修复线上严重 Bug 的方法。

Hotpatch 框架为 MPCommandCenter 模块，包含指令推送与动态修复两个模块。

`指令推送`：负责与服务器同步脚本，控制脚本的运行版本和运行时机。

`动态修复`：执行 Lua 脚本，替换 Objective-C 方法完成 Hotpatch。

### 1.1.2 Hotpatch 工作过程

* 程序启动，执行已经下载并存储到本地的脚本。

* 程序启动，与服务器通信并下载脚本，下载成功后会立刻执行新的脚本。

* 程序从后台切回前台，与服务器通信并同步脚本，下载成功后会在下次启动时执行。

## 1.2 发布脚本

开发者本地验证通过后，在服务端配置需要发布的脚本。可以设置推送百分比以及推送的用户 ID。（该功能正在开发中）

### 1.2.1 脚本验签

为了保障安全性，Hotpatch 有自定义的验签流程，保证脚本来源的正确性。验签流程如下：

* 读取Lua 脚本内容，对内容字符串做 md5 加密。
* 以二进制格式读取Lua 脚本内容，对二进制内容做 AES128 加密。
* 创建 zip 文件，将上述两项内容添加到 zip 文件中。
* 以二进制格式读取上述 zip 文件，再进行一次 AES128 加密，加密后数据保存为 zip 文件。
* 以二进制格式读取上述 zip 文件，使用 SHA1 算法，做一次签名。
* 以二进制格式读取上述 zip 文件，加上数据分隔符，加上签名数据，组成 js 脚本。

客户端获取 js 脚本，也会使用以上反顺序得到可以真正运行的脚本。

### 1.2.2 Lua2Js 工具使用方法

一、 生成自己的 Lua2Js 工具



二、 使用方法

* 点击“选择 lua 脚本”按钮
![ca33c9ec32722af65948a0584d52ec7a](https://os.alipayobjects.com/rmsportal/sNQPUqUdCqiMCUw.png)

* 选择需要转换的 lua 脚本，点击“ open”按钮
![87e5307d18a4d50a177ce7df0447d77e](https://t.alipayobjects.com/images/rmsweb/T1cUxfXcJfXXXXXXXX.png)

* 成功转化 js 脚本
![85960aa78c570154e4add0e9fb61d459](https://t.alipayobjects.com/images/rmsweb/T1R_RfXlVqXXXXXXXX.png)

## 1.3 客户端接入 Hotpatch

### 1.3.1 管理对称加密 Key

实现 mPaasAppInterface 的下面接口，使 Hotpatch 模块可以回调接入应用，获知使用配置在无线保镖中的哪个 key 进行加密处理。（需要应用将 key 与 secret 配置到无线保镖中管理）

```
#pragma mark Hotpatch

/**
 *  配置在无线保镖中的，用于 Hotpatch 脚本加密的 key 的名字。Hotpatch 模块拿到这个 key，会调用无线保镖的接口进行加密。
 *
 *  @return 配置在无线保镖中的 key 的名字
 */
- (NSString*)appHotpatchScriptEncryptionKey;
```

### 1.3.2 管理非对称加密公钥文件

调用 APMobileSecurity 库的方法初始化自己的公钥文件，请在 main 函数中调用。

```
APSecBufferRef APSecGetPKS()
{
    char sig[] = {};
    
    size_t len = sizeof(sig);
    APSecBufferRef buf = (APSecBufferRef)malloc(sizeof(APSecBuffer) + len);
    buf->length = len;
    memcpy(buf->data, sig, len);
    return buf;
}

int main(int argc, char *argv[])
{
    NSString *path;
    APSecBufferRef buf;
    int ret;
    
    path = [[NSBundle mainBundle] pathForResource:@"pubkey" ofType:@"pem"];
    APSecInitPublicKey([path UTF8String]);
    
    buf = APSecGetPKS();
    ret = APSecVerifyFile([path UTF8String], buf->data, buf->length);
    free(buf);
    if (ret != 0) {
        NSLog(@"The public key is modified.");
    }
    
    @autoreleasepool {
        mPaasInit(@"12345", [mPaasAppInterfaceImp class]);
        return UIApplicationMain(argc, argv, @"DFApplication", @"DFClientDelegate");
    }
}
```