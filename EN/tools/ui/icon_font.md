@cnName 3 IconFont
@priority 3

# 3 IconFont

[TOC]

## 3.1 Introduction

iOS applications will carry many PNG icon resources. You need to package multiple sets of resources for different screen sizes.  To reduce the icon resources and improve display effects, IconFont is based on vectorgraphs and offers alternative solutions for some icon resources. It can effectively downsize the install package. 

The source files of IconFont are font files in the ttf format. The ttf file describes the vector information of each icon.  In usage, mPaaS will load the ttf fonts and the business-level codes will be able to draw the icon. 

More resources can be found and downloaded on the home page of IconFont:   http://iconfont.cn/

## 3.2 How to use

### 3.2.1 Add IconFont

#### 3.2.1.1 Description file

After the MPCommonUI module is imported to mPaaS, you need to add APCommonUI.bundle to the project.  Under the bundle, there is an 'ap_iconfont_name.plist' for configuring the IconFont. 

Each resource contains two fields: the font file and the configuration file, as shown in the figure: 
![e75a42aa60489c90bb1f6762429da163](https://t.alipayobjects.com/images/rmsweb/T1YDFfXilvXXXXXXXX.png)

The resource name is the 'Family Name' of the font. The 'file' indicates the location of the ttf font file relative to 'main bundle', and the 'config' indicates the location of the description file relative to 'main bundle'. Note that both of them do not require the extension. 

#### 3.2.1.2 Viewing family name of font

Open the ttf file in FontLab Studio, and query the font's 'Family Name'. 
![8735a5e5cf3c74d9e0124fc7ba83dbed](https://t.alipayobjects.com/images/rmsweb/T12E0fXglaXXXXXXXX.png)

### 3.2.2 IconFont interface

The text value for drawing the IconFont is an 8-bit Unicode character.  mPaaS provides UIView extensions to display IconFont conveniently. 

```
@interface APIconFont : NSObject

//Use defeult ttf (the first item in the configuration file)
+ (UIFont *)iconFont;
+ (UIFont *)iconFontWithSize:(NSInteger)fontSize;

+ (UIFont *)iconFontWithFontName:(NSString *)fontName;
+ (UIFont *)iconFontWithFontName:(NSString *)fontName size:(NSInteger)fontSize;

@end

@interface UIView (APIconFont)

//Use defeult ttf (the first item in the configuration file)
+ (UIView *)iconFontViewWithPoint:(CGPoint)point name:(NSString *)name;
+ (UIView *)iconFontViewWithPoint:(CGPoint)point name:(NSString *)name color:(UIColor *)color;

+ (UIView *)iconFontViewWithPoint:(CGPoint)point name:(NSString *)name fontName:(NSString *)fontName;
+ (UIView *)iconFontViewWithPoint:(CGPoint)point name:(NSString *)name fontName:(NSString *)fontName color:(UIColor *)color;

- (BOOL)setIconFontColor:(UIColor *)color;
- (BOOL)setIconFontName:(NSString *)name;

@end
```

### 3.2.3 Drawing IconFont

```C
UILabel * label = [[UILabel alloc] init];
label.text = @"\U00003439";
label.font = [APIconFont iconFont];
[self.view addSubview:label];

UIView *fontView = [UIView iconFontViewWithPoint:CGPointMake(100, 100) name:@"General"];
//Set icon position and size dynamically with the frame. 
fontView.frame = CGRectMake(100, 100, 48, 48);
[self.view addSubview:fontView];

fontView = [UIView iconFontViewWithPoint:CGPointMake(130, 100) name:@"Costume"];
//Reset color. 
[fontView setIconFontColor:[UIColor redColor]];
//Reset image. 
[fontView setIconFontName:@"yuebao"];
[self.view addSubview:fontView];
```

