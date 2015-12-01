@cnName 描述文件
@priority 1

# 描述文件

[TOC]

## 1 简介

通用控件支持主题式管理，每个主题为一个独立的 bundle。这个目录里面包含主题描述文件`theme.plist`和其它文件、图片资源。

主题目录可以放在Main Bundle里随应用下发，也可以后期下载后放在Documents里。

## 2 类接口介绍

### 2.1 APThemeManager

```
#define APDefaultTheme [APThemeManager sharedInstance].defaultTheme
#define APCurrentTheme [APThemeManager sharedInstance].currentTheme

@interface APThemeManager : NSObject

@property (nonatomic, strong, readonly) APTheme* defaultTheme;
@property (nonatomic, strong, readonly) APTheme* currentTheme;

+ (instancetype)sharedInstance;

/**
 *  加载主题，并作为Current Theme
 *
 *  @param path 主题.bundle的资源路径
 */
- (void)loadThemeWithPath:(NSString*)path;

@end
```

`defaultTheme`：默认主题，放在Main Bundle里，名字为`UIDefaultTheme.bundle`。
`currentTheme`：当前主题，当使用默认主题时同`defaultTheme`。

### 2.2 APThemeValuePath

```
@protocol APThemeValuePath <NSObject>

- (NSString*)stringForPath:(NSString*)path;
- (UIColor*)colorForPath:(NSString*)path;
- (NSInteger)intForPath:(NSString*)path;
- (float)floatForPath:(NSString*)path;
- (BOOL)boolForPath:(NSString*)path;
- (UIImage*)imageForPath:(NSString*)path;
- (UIFont*)fontForPath:(NSString*)path;

// 某个路径的值是否在配置文件里定义了
- (BOOL)definedForPath:(NSString*)path;

@end
```

@path参数为`.`分隔的路径，对应`theme.plist`的字典树结构。

### 2.3 APTheme

```
@interface APTheme : NSBundle <APThemeValuePath>

@property (nonatomic, strong, readonly) NSString* name;
@property (nonatomic, strong, readonly) NSDictionary* theme;
@property (nonatomic, assign, readonly) BOOL inherited; // 当某个值找不到时，是否继续搜索默认主题

- (instancetype)initWithPath:(NSString*)path;

- (APThemeFetch*)fetchForPrefix:(NSString*)prefix;

@end
```

APTheme实现`APThemeValuePath`这个接口的方法。

### 2.4 APThemeFetch

```
@interface APThemeFetch : NSObject <APThemeValuePath>
@end
```

APThemeFetch实现`APThemeValuePath`这个接口的方法。这个类由APTheme的fetchForPrefix方法创建。用法如下：

```
APThemeFetch* fetch = [APCurrentTheme fetchForPrefix:@"APNavigationBar.Global.Background"];

if ([fetch definedForPath:@"Image"]) {
    UIImage* image = [[fetch imageForPath:@"Image"] stretchableImageWithLeftCapWidth:2 topCapHeight:0];
    if (image)
        [[UINavigationBar appearance] setBackgroundImage:image forBarMetrics:UIBarMetricsDefault];
}
if ([fetch definedForPath:@"BarTintColor"]) {
    [[UINavigationBar appearance] setBarTintColor:[fetch colorForPath:@"BarTintColor"]];
}
```
上面fetch获取的数据实际为：

`APNavigationBar.Global.Background.Image`
`APNavigationBar.Global.Background.BarTintColor`

## 3 描述文件格式

描述文件为一个plist文件，根节点下面预定义了`Name`和`Constants`两项。其它项目可以自定义，但通常为每个UI组件建立一个节点，并保证下级属性定义结构合理。
![image1](https://t.alipayobjects.com/images/rmsweb/T183XiXaJbXXXXXXXX.png)

`Name`：主题的名称

`Constants`：这个配置文件可以使用的常量，可以理解为代码中的宏。例如主题会有一些公共的颜色值，字号定义，建议将这些定义抽出来放在Constants里。这样修改起来会更加方便。比如上图定义了AntBlue这个颜色值的常量，那么配置文件里就可以使用`$AntBlue`来引用这个常量，而不需要每个使用这个颜色值的地方都写`#00aaee`。

### 3.1 逻辑判断

有些时候，我们对不同设备可能使用不同的图片，不同的字号。只需要在配置文件里将指定路径的值的类型修改为`Dictionary`，并在这个字典里定义`Default`值和其它逻辑判断条件满足时的值即可。比如：
![image2](https://t.alipayobjects.com/images/rmsweb/T1l28iXn4bXXXXXXXX.png)

这个图里定义的字体，当设备屏幕不小于iPhone6 plus时，使用20号字，其它情况都使用17号。如果定义了多个判断条件，那么当设备参数都符合时结果是不确定的。

目前支持的判断方法如下：A?B。其中A支持的情况为

|主语（A）|支持的运算符（?）|值的情况（B）|例子|说明|
|-|-|-|-|-|
|screen|<, <=, =, >, >=, !=|pad_low<br>pad_retina<br>phone_low<br>phone4<br>phone5<br>phone6<br>phone6p|screen>phone5<br>screen<=phone6p|pad_low表示低分辨率的iPad屏幕，phone_low表示低分辨率的iPhone屏幕。iPhone屏幕和iPad屏幕不能比较大小。比如本设备是iPad4，虽然为高分屏，但screen>phone4这个表达式是不会被解析为True的。|
|ios|<, <=, =, >, >=, !=|8<br>8.2<br>9.0.1|ios<8.0<br>ios>=9.0.1|表达式右边的版本表达式最多支持三段结构，只写一段或两段也可以。逐位进行比较。|
|device|=, !=|iphone<br>ipad|device=iphone<br>device!=ipad|只支持等于或不等于两个运算符|

### 3.2 颜色定义

支持`#rrggbb`和`#rrggbbaa`两种格式定义颜色，使用16进制。如果没指定`aa`分量，会默认为1.0，也就是不透明。

### 3.3 字体定义

可以使用`APThemeValuePath`接口的`fontForPath:`方法直接获取一个UIFont*对象。字体下面目前支持`Bold`和`Size`两个属性。其中`Bold`为可选的，默认为NO；`Size`为字号，定义为String或Number都行。如图：
![image3](https://t.alipayobjects.com/images/rmsweb/T1l28iXn4bXXXXXXXX.png)

### 3.4 图片定义

你可以从配置文件里获得字符串并自己加载图片，不过推荐使用imageForPath接口来加载图片。在配置文件里只需要配置图片相对主题Bundle的路径即可。