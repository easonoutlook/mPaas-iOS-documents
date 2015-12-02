@cnName 自定义主题
@priority 2

# 自定义主题

MPCommonUI 模块中会自带一份默认主题。当接入方应用有自己的样式需求时，可以创建自己的主题资源目录，并使用 APThemeManager 加载相应主题即可。

APCommonUI.bundle 这个目录是通用控件模块自带的资源包，里面包含了默认主题。请尽量不要修改这个目录里的图片与主题文件，以防升级 MPCommonUI 模块时带来不必要的资源覆盖问题。

如下图：
![image](https://os.alipayobjects.com/rmsportal/OvRDrtKsfQFEdLQ.png)

<font color=green>绿色</font>部分为移动框架自动维护的目录，里面有默认的资源包，___这里不要修改___。我们在自己的工程中添加一个资源包，同时创建一个`theme.plist`文件，内容如图<font color=blue>蓝色</font>。在<font color=red>红色</font>部分定义：

```
Inherited = YES
```
表示这个主题是继承自默认主题的，对该主题文件中未定义的值，会使用 APCommonUI.bundle 中的默认主题的值。

在程序启动的时候加载我们的主题（这个代码通常可以放在应用的 mPaasAppInterface 实现类的 init 方法中）：

```
[[APThemeManager sharedInstance] loadThemeWithPath:[[NSBundle mainBundle] pathForResource:@"MyAppTheme" ofType:@"bundle"]]
```