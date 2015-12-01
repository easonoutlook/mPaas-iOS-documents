@cnName 3 IconFont
@priority 3

# 3 IconFont

[TOC]

## 3.1 简介

iOS 应用会携带许多 PNG 格式的图标资源，还要对不同屏幕尺寸打包多套资源。为了减少图标资源、提升显示效果，IconFont 基于适量图形，提供部分图标资源的替代方案，有效减少安装包大小。

IconFont 的源文件为 ttf 格式的字体文件，ttf 文件里描述了每个图标的矢量信息。在使用时，mPaaS 会将 ttf 字体加载出来，业务代码便可以绘制图标。

IconFont 主页，内有大量资源下载： http://iconfont.cn/

## 3.2 使用方法

### 3.2.1 添加 IconFont

#### 3.2.1.1 描述文件

引入 mPaaS 通用控件模块 MPCommonUI 后，需要将 APCommonUI.bundle 添加到工程里。这个 Bundle 下有`ap_iconfont_name.plist`文件来配置 IconFont。

每个资源包括两个字段，分别为字体文件与配置文件，如图：
![e75a42aa60489c90bb1f6762429da163](https://t.alipayobjects.com/images/rmsweb/T1YDFfXilvXXXXXXXX.png)

资源名为字体的`Family Name`，`file`为字体 ttf 文件相对`main bundle`的位置，`config`是描述文件相对`main bundle`的位置，注意都不需要添加扩展名。

#### 3.2.1.2 查看字体的 Family Name

使用 FontLab Studio 打开 ttf 文件，查询它的`Family Name`。
![8735a5e5cf3c74d9e0124fc7ba83dbed](https://t.alipayobjects.com/images/rmsweb/T12E0fXglaXXXXXXXX.png)

### 3.2.2 IconFont 接口

绘制 IconFont 时的 text 文本值为 8 位的 Unicode 字符。mPaaS 提供了 UIView 扩展来方便的显示 IconFont。

```
@interface APIconFont : NSObject

//使用默认 ttf(配置文件第一项)
+ (UIFont *)iconFont;
+ (UIFont *)iconFontWithSize:(NSInteger)fontSize;

+ (UIFont *)iconFontWithFontName:(NSString *)fontName;
+ (UIFont *)iconFontWithFontName:(NSString *)fontName size:(NSInteger)fontSize;

@end

@interface UIView (APIconFont)

//使用默认 ttf(配置文件第一项)
+ (UIView *)iconFontViewWithPoint:(CGPoint)point name:(NSString *)name;
+ (UIView *)iconFontViewWithPoint:(CGPoint)point name:(NSString *)name color:(UIColor *)color;

+ (UIView *)iconFontViewWithPoint:(CGPoint)point name:(NSString *)name fontName:(NSString *)fontName;
+ (UIView *)iconFontViewWithPoint:(CGPoint)point name:(NSString *)name fontName:(NSString *)fontName color:(UIColor *)color;

- (BOOL)setIconFontColor:(UIColor *)color;
- (BOOL)setIconFontName:(NSString *)name;

@end
```

### 3.2.3 绘制 IconFont

```C
UILabel * label = [[UILabel alloc] init];
label.text = @"\U00003439";
label.font = [APIconFont iconFont];
[self.view addSubview:label];

UIView *fontView = [UIView iconFontViewWithPoint:CGPointMake(100, 100) name:@"一般"];
//通过 frame 动态设置 icon 位置和大小
fontView.frame = CGRectMake(100, 100, 48, 48);
[self.view addSubview:fontView];

fontView = [UIView iconFontViewWithPoint:CGPointMake(130, 100) name:@"服饰"];
//重置颜色
[fontView setIconFontColor:[UIColor redColor]];
//重置图片
[fontView setIconFontName:@"yuebao"];
[self.view addSubview:fontView];
```