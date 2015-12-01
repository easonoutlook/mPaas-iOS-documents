@cnName APNavigationBar
@priority 3

# APNavigationBar

当使用通用控件库时，客户端框架会使用APNavigationBar作为自己的导航条类。下面`[]`中间为可选属性。

```
StatusBarStyle
APNavigationBar
	Global
    	Title
        	TextColor
            Font
            	[Bold]
                Size
        Background
        	[Image]
            [TintColor]
        	[BarTintColor]
            [Translucent]
    Button
    	[BackImage]
        TextColor
        Font
        	[Bold]
        	Size
    Background
    	Color
        Translucent
        [Image]
    
```
`StatusBarStyle`：因为在iOS上导航条与状态条的颜色搭配息息相关，所以与APNavigationBar并列可以定义状态条的样式。取值详见系统`UIStatusBarStyle`枚举的定义。0为深色样式，用于淡颜色的导航条；1为浅色样式，用于深色的导航条。

`Global`：UINavigationBar控件全局设置，影响应用的所有UINavigationBar及其的子类。通常用来统一系统提供的窗口的导航条样式，如通讯录联系人选择、选择相册照片的窗口。`Global.Title`设置导航条中间的标题字体与颜色，`Global.Background`设置导航条背景，可以设置图片或指定颜色。

`Button`：导航条的返回按钮属性。`BackImage`为返回按钮的图片，可以不指定而使用系统默认样式。

`Background`：除了上面`Global`设置全局的导航条外，可以单独指定`APNavigationBar`的背景。

一个定义`APNavigationBar`的样例如下图：
![image](https://t.alipayobjects.com/images/rmsweb/T1Tv0iXXNdXXXXXXXX.png)