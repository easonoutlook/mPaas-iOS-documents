@cnName 6 附录
@priority 6

# 6 附录

[TOC]

## 6.1 用户态处理

第三方应用会有自己的账号登录系统，而部分移动组件需要获取接入应用的 userId 才能正常工作。接入应用在自己的登录态发生改变时，请使用下面方法通知移动组件。（包括用户登出事件，请传 nil）

### 6.1.1 通知统一存储
当使用了统一存储时，请第一时间通知统一存储，让统一存储进行用户数据库的切换，再通知其它业务层
```C
[[APDataCenter defaultDataCenter] setCurrentUserId:userId];
```
当用户登出时，可以不调用 setCurrentUserId 方法，统一存储会继续打开上一个用户的数据库，但这没有什么问题。

### 6.1.2 通知监控埋点
```C
[[APMonitorPointManager sharedInstance] setAuthenticated:YES];
[[APMonitorPointManager sharedInstance] setUserId:userId];
```

### 6.1.3 添加 mPaasAppInterface 回调
mPaasAppInterface 这个 Protocol 里有 currentUserId 方法，这是一些移动模块用来向接入应用请求当前用户 userId 的方法。
```C
- (NSString*)currentUserId
{
    return userId;
}
```

## 6.2 屏蔽 NSLog 输出

开发阶段我们会允许自己的应用输出控制台日志，但是发版后希望有快捷方法屏蔽所有日志输出。请在你的应用中添加如下代码，并用宏控制，在编译 App Store 版本时重定向日志方法为空方法。

```
#include "stdio.h"

// 非 DEBUG 模式将日志关闭
#ifndef DEBUG
#define HIDE_ON_RELEASE 1
#endif

// 是否需要把 Log 中的转义字符转成中文
#ifdef DEBUG
#define CONVERT_UNICODE_ESCAPES
#endif

#if HIDE_ON_RELEASE
void NSLogv (NSString *format, va_list args) { }
int printf (const char *format, ...) { return 0; }
#endif

#ifdef CONVERT_UNICODE_ESCAPES
// 使用 NSLog 打印NSArray 或 NSDictionary 时，里面的非 ASCII 字符会显示为"\U64cd\U4f5c\U6210\U529f"这种形式，这个方法把所有\U 表示的 unichar 转换为相应的字符。为保证效率，写得较复杂。
static NSString* convertUnicodeCharEscapes(NSString* source)
{
    const NSInteger MAX_UNICHAR_LEN = 100;
    NSInteger lenSource = [source length];
    unichar unicharBuf[MAX_UNICHAR_LEN]; // 一次最多转 100 个
    NSMutableString* dest = [[NSMutableString alloc] initWithCapacity:lenSource];
    NSInteger i = 0, copyStart = 0, slashPos = -2, escapeIndex = -1, escapePow = 0, escapeCharIndex = 0, escapeCharValue = 0;
    
    while (i < lenSource)
    {
        unichar c = [source characterAtIndex:i];
        if (escapeIndex >= 0)
        {
            if (escapeIndex == 3)
            {
                escapeCharValue = 0;
                escapePow = 16 * 16 * 16;
            }
            
            // 16 进制转换
            if (c >= '0' && c <= '9')
                escapeCharValue += escapePow * (c - '0');
            else if (c >= 'a' && c <= 'f')
                escapeCharValue += escapePow * (c - 'a' + 10);
            else
            {
                // someting wrong
                if (escapeCharIndex > 0)
                    [dest appendString:[[NSString alloc] initWithCharactersNoCopy:unicharBuf length:escapeCharIndex freeWhenDone:NO]];
                copyStart = i - (5 - escapeIndex);
                escapeIndex = -1;
                i ++;
                continue;
            }
            
            if (escapeIndex == 0)
            {
                unicharBuf[escapeCharIndex] = (unichar)escapeCharValue;
                copyStart = i + 1;
                // 判断接下来还是不是\U
                if (escapeCharIndex < MAX_UNICHAR_LEN - 1 && i < lenSource - 2 && [source characterAtIndex:i + 1] == '\\' && [source characterAtIndex:i + 2] == 'U')
                {
                    escapeCharIndex ++;
                    escapeIndex = 3;
                    i += 2;
                }
                else
                {
                    escapeIndex = -1;
                    [dest appendString:[[NSString alloc] initWithCharactersNoCopy:unicharBuf length:escapeCharIndex + 1 freeWhenDone:NO]];
                }
            }
            else
            {
                escapeIndex --;
                escapePow /= 16;
            }
        }
        else if (c == '\\')
        {
            slashPos = i;
        }
        else if (c == 'U')
        {
            if (slashPos == i - 1)
            {
                escapeIndex = 3;
                escapeCharIndex = 0;
                if (i - 2 - copyStart + 1 > 0)
                {
                    [dest appendString:[source substringWithRange:NSMakeRange(copyStart, i - 2 - copyStart + 1)]];
                    copyStart = i - 1;
                }
            }
        }
        i ++;
    }
    
    if (copyStart == 0)
        return source;
    else if (lenSource - copyStart > 0)
        [dest appendString:[source substringWithRange:NSMakeRange(copyStart, lenSource - copyStart)]];
    return dest;
}
#endif

static void _NSLog(id NIL, ...)
{
    va_list args;
    va_start(args, NIL);
    NSLogv(@"%@", args);
    va_end(args);
}

void NSLog (NSString *format, ...)
{
#if HIDE_ON_RELEASE
    // EMPTY REWRITE
#else
    va_list args;
    va_start(args, format);
    NSString *logString = [[NSString alloc] initWithFormat:format arguments:args];
    va_end(args);
#ifdef CONVERT_UNICODE_ESCAPES
    logString = convertUnicodeCharEscapes(logString);
#endif
    _NSLog(nil, logString); // 这么弄一下，要不然不知道怎么传给 NSLogv
#endif
}
```


## 6.3 第三方库列表

| SDK |英文名|功能|版本|下载地址|
|-|-|-|-|-|
|无线保镖| SecurityGuardSDK |黑盒保存加密数据、密钥| 1.5.32 |[下载](https://t.alipayobjects.com/L1/92/1333/1438831597529.tar.gz)|
|设备唯一标识| UTDID |生成设备唯一标识| 1.0.2 |[下载](https://t.alipayobjects.com/L1/92/1062/1438831737134.zip)|
| OpenSSL | APOpenSSL | OpenSSL 轻量级封装| 1.0.0 |[下载](https://t.alipayobjects.com/L1/92/1066/1438831924914.zip)|