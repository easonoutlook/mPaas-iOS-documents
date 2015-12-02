@cnName 6 Appendix
@priority 6

# 6 Appendix

[TOC]

## 6.1 User-mode Processing

Third-party applications of mPaaS have their own account login systems, and some components of mPaaS require the userId of the accessed application for normal operation.  In case of changes in the login status of the accessed application, the application should notify mPaaS using the method below.  (User sign-out events are also included. For logout events, pass nil.) 

### 6.1.1 Notifying Data Center
When Data Center is used, the application should notify Data Center of changes in the login status as soon as possible for Data Center to switch user databases. Then the application can notify other service layers of the change. 
```C
[[APDataCenter defaultDataCenter] setCurrentUserId:userId];
```
When a user signs out, you don't have to call the setCurrentUserId method. Data Center will continue to open the database of last user, which will not cause problems. 

### 6.1.2 Notifying monitor point
```C
[[APMonitorPointManager sharedInstance] setAuthenticated:YES];
[[APMonitorPointManager sharedInstance] setUserId:userId];
```

### 6.1.3 Adding mPaasAppInterface callback
The mPaasAppInterface protocol contains the currentUserId method, which will be used by some modules of mPaaS for requesting the current userId of the accessed application. 
```C
- (NSString*)currentUserId
{
	return userId;
}
```

## 6.2 Blocking NSLog output

During the development phrase we will allow the output of console logs of the developer's own applications. Yet after the application is distributed, we expect to offer quick ways to block all the log output.  Add the codes below to your application and control it with macros. The log redirection method in compiling an App Store version is empty.  

```
#include "stdio.h"

// In non-DEBUG mode, the log will be disabled. 
#ifndef DEBUG
#define HIDE_ON_RELEASE 1
#endif

// Whether to convert the escape characters in the log to Chinese characters. 
#ifdef DEBUG
#define CONVERT_UNICODE_ESCAPES
#endif

#if HIDE_ON_RELEASE
void NSLogv (NSString *format, va_list args) { }
int printf (const char *format, ...) { return 0; }
#endif

#ifdef CONVERT_UNICODE_ESCAPES
// When NSLog is used for outputting NSArray or NSDictionary, the non-ASCII characters it contains will be displayed in the form of "\U64cd\U4f5c\U6210\U529f". This method converts all the unichar indicated with "\U" to corresponding characters.  ...
static NSString* convertUnicodeCharEscapes(NSString* source)
{
    const NSInteger MAX_UNICHAR_LEN = 100;
    NSInteger lenSource = [source length];
    unichar unicharBuf[MAX_UNICHAR_LEN]; // A maximum of 100 unichar characters can be converted at one time. 
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
            
            // Convert to the hexadecimal character. 
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
                // Judge whether \U still exists. 
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
    _NSLog(nil, logString); // This is necessary. Or else it is hard to determine how to pass the value to NSLogv. 
#endif
}
```


## 6.3 List of third-party library

| Chinese Name |English Name|Function|Version|Download URL|
|-|-|-|-|-|
|无线保镖| SecurityGuardSDK |A black box storing the encrypted data and keys| 1.5.32 |[Download](https://t.alipayobjects.com/L1/92/1333/1438831597529.tar.gz)|
|设备唯一标识| UTDID |To generate the unique ID of the device| 1.0.2 |[Download](https://t.alipayobjects.com/L1/92/1062/1438831737134.zip)|
| OpenSSL | APOpenSSL | OpenSSL lightweight encapsulation| 1.0.0 |[Download](https://t.alipayobjects.com/L1/92/1066/1438831924914.zip)|
|设备指纹| DevicePrint |Device fingerprint| 1.2.0 |[Download](https://t.alipayobjects.com/L1/92/1064/1438832508166.zip)|

