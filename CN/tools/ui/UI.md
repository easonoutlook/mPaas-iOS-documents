@cnName 1 UI 控件
@priority 1

# 1 UI 控件

[TOC]

## APActionSheet
类名： APActionSheet，继承 UIActionSheet

描述：定义 ActionSheet 基类，统一管理 ActionSheet

接口：

```C
+(void)setIsBackGroundMode:(bool)isBackGroundMode;
```

属性：

```C
@property(nonatomic,weak)id<UIActionSheetDelegate>dtDelegate;
```

示例：

```C
- (void)setupActionSheetWithTitle:(NSString *)title
{
    if (self.mobileContact){
        _contactActionSheet = [[APActionSheet alloc] initWithTitle:title delegate:self cancelButtonTitle:nil destructiveButtonTitle:nil otherButtonTitles:nil];
        NSArray *ary = [self.mobileContact phoneAry];
        ary = (ary != nil) ? ary : [self.mobileContact accountIdAry];
        for (NSString *content in ary) {
            [_contactActionSheet addButtonWithTitle:content];
        }
        // 取消按钮放到最后
        [_contactActionSheet addButtonWithTitle:TRANSFER_TEXT_TO_ACCOUNT_SHEET_CANCEL];
        [_contactActionSheet showInView:self.view];
    }
}
```

##  APActionSheetManager
类名： APActionSheetManager，继承 NSObject

描述：对 APActionSheet 进行管理。

接口：

```C
/**
  * 构建一个单例类
*/

+ (APActionSheetManager *)sharedAPActionSheetManager;

/**
  *删除所有的 actionsheet
*/
-(void)dismissAllUIActionSheet;

/**
  *将 actionsheet 添加到显示队列里面
*/
-(void)addToShowedPool:(APActionSheet*)alertView;

````

属性：

```C
@property(nonatomic,assign)bool backGroundMode;
```

示例：

```C
APActionSheetManager *manager = [APActionSheetManager sharedAPActionSheetManager];
```

##  APAgreementBox
类名： APAgreementBox，继承 UIControl

描述：用于显示用户注册协议的组件

接口：

```C
/**
 *  用于显示用户注册协议的组件
 *  @param frame     视图在父视图的位置和大小
 *  @param labelText 标题
 *  @param linkText  超链接标题
 *  @return 新创建并经过初始化的注册协议组件
 */
- (id)initWithFrame:(CGRect)frame labelText:(NSString *)labelText linkText:(NSString *)linkText;

// 设置是否显示单选框
- (void)setCheckBoxHidden:(BOOL)hidden;
```

属性：

```C
// 用户点击单选框
@property(nonatomic, readonly) APCheckBox *checkBox;
// 用户点击超链接按钮
@property(nonatomic, readonly) APLinkButton *linkButton;
```

示例：

```C
APAgreementBox *agreementBox = [APAgreementBox alloc] initWithFrame:15
                                                          labelText:@"同意"
                                                           linkText:@"《支付宝注册协议》"];
[self.view addSubview:agreementBox];
```

##  APAlertView
类名： APAlertView, 继承自 UIAlertView

描述： 定制的 UIAlertView, 只有两个按钮时按钮将上下排列

使用： 同UIAlertView；请在需要两个按钮上下排列时使用此类，其它情况下仍使用 UIAlertView

示例：

```C
APAlertView *alertView = [[APAlertView alloc] initWithTitle:@"新消息"
                                                    message:@"我们将推送给您与您的账户相关的新提醒、新服务、帮助等。"
                                                   delegate:self
                                          cancelButtonTitle:@"我知道了"
                                          otherButtonTitles:@"使用遇到问题", nil];
[alertView show];
```

##  APAlertViewManager
类名： APAlertViewManager，继承 NSObject

描述：对 APAlertView 进行管理。

接口：
```C
/**

  * 构建一个单例类

*/

+ (APAlertViewManager *)sharedAPAlertViewManager;

/**
  *删除所有的 alertView
*/
- (void)removeAllAlertView;

/**
  *将 alertView 添加到显示队列里面
*/
- (void)pushAPAlertView:(UIAlertView *)alertview;
```
属性：

```C
@property(nonatomic,assign)bool isBackGroundMode;
```

示例：

```C
APAlertViewManager *manager = [APAlertViewManager sharedAPAlertViewManager];
```

##  APBankCardTextField
类名： APBankCardTextField，继承 APTextField

描述：银行卡文本输入框

接口：参考 APTextField

示例：

```C
APBankCardTextField *textField = [[APBankCardTextField alloc] initWithFrame:UIEdgeInsetsInsetRect(self.bounds, UIEdgeInsetsMake(0, 0, 1, 0))];
textField.validator = [APTextValidator bankCardValidator];
```

##  APBarButtonItem
类名： APBarButtonItem，继承 UIBarButtonItem

描述： APBarButtonItem 是放置在 UIToolbar 或 UINavigationBar 上的按钮对象，可以响应用户的操作。

接口：

```C
/**
 *  @return 新创建 UIBarButtonSystemItemFlexibleSpace 类型对象，用于调整按钮间距
 */
+ (APBarButtonItem *)flexibleSpaceItem;

/**
 *  用于创建并初始化一个“返回”的按钮对象
 *  @param title    铵钮标题，注意：设置没有效果，内部使用默认的返回图片
 *  @param target   响应按钮点击事件的对象
 *  @param action   响应按钮点击事件的函数
 *  @return 新创建并经过初始化的“返回”按钮对象
 */
+ (APBarButtonItem *)backBarButtonItemWithTitle:(NSString *)title target:(id)target action:(SEL)action

/**
 *  用于创建并初始化一个指定图片的按钮对象。
 *  @param image    铵钮图片
 *  @param target   响应按钮点击事件的对象
 *  @param action   响应按钮点击事件的函数
 *  @return 新创建并经过初始化的指定图片的按钮对象。
 */
- (id)initWithImage:(UIImage *)image style:(UIBarButtonItemStyle)style target:(id)target action:(SEL)action

/**
 *  用于创建并初始化一个指定标题的按钮对象。
 *  @param title    铵钮标题
 *  @param target   响应按钮点击事件的对象
 *  @param action   响应按钮点击事件的函数
 *  @return 新创建并经过初始化的指定标题的按钮对象。
 */
- (id)initWithTitle:(NSString *)title style:(UIBarButtonItemStyle)style target:(id)target action:(SEL)action
```
示例：

```C
APBarButtonItem *item = [[APBarButtonItem alloc] initWithImage:[UIImage imageNamed:@"barbutton"] style:0 target:self action:@selector(didReceiveMemoryWarning)];
self.navigationItem.rightBarButtonItem = item;

APBarButtonItem *item = [[APBarButtonItem alloc] initWithTitle:@"账单" style:0 target:self action:@selector(didReceiveMemoryWarning)]; 
self.navigationItem.rightBarButtonItem = item;
```

##  APBorderedButton
类名： APBorderedButton，继承 UIButton

描述：目前默认实现了一下三种样式的按钮：

主按钮；  
次按钮；
自定义按钮，不会应用任何样式，也不会初始化 frame

接口：
```C

/**
 *  一个方法的辅助方法，用于创建并初始化一个按钮的对象
 *
 *  @param buttonType 按钮类型，必须是定义在<code>APBorderedButtonType</code>中的其中一个值
 *  @param title      铵钮标题
 *  @param target     响应按钮点击事件的对象
 *  @param action     响应按钮点击事件的函数
 *
 *  @return 新创建并经过初始化的按钮对象
 *
 *  使用这个方法创建的按钮对象，其默认的 frame 为<code>CGRectMake(0.0, 0, 标题宽度+20, 26.0)</code>，
 *  对于指定的 target 和 action 所对应事件为<code>UIControlEventTouchUpInside</code>
 */
+ (APBorderedButton *)buttonWithType:(APBorderedButtonType)buttonType
                               title:(NSString *)title
                              target:(id)target
                              action:(SEL)action;

/**
 *  用于创建并初始化一个按钮的对象
 * 
 *  @param title      按钮标题
 *  @param buttonType 按钮类型，必须是定义在<code>APBorderedButtonType</code>中的其中一个值
 *
 *  @return 新创建并经过初始化的按钮对象
 */
- (id)initWithButtonTitle:(NSString *)title buttonType:(APBorderedButtonType)buttonType;

```
示例：
```C
APBorderedButton *confirmBtn = [APBorderedButton buttonWithType:APBorderedButtonTypeDefault title:confirmButtonTitle target:self action:@selector(onConfirm:)];
```

##  APButton
类名： APButton，继承 UIButton

描述：目前默认实现了一下三种样式的按钮：

主按钮；  
次按钮；
警示按钮，通常按钮背景会显示为较醒目的红色；
自定义按钮，不会应用任何样式，也不会初始化 frame

接口：
```C
/**

 *  一个方法的辅助方法，用于创建并初始化一个按钮的对象。

 *  @param buttonType 按钮类型，必须是定义在 APButtonType 中的其中一个值。

 *  @param title      铵钮标题

 *  @param target     响应按钮点击事件的对象

 *  @param action     响应按钮点击事件的函数

 *  @return 新创建并经过初始化的按钮对象。

 *  使用这个方法创建的按钮对象，其默认的 frame 为 CGRectMake(15.0, 0, 290.0, 42.0)，

 *  对于指定的 target 和 action 所对应事件为 UIControlEventTouchUpInside。

 */
+ (APButton *)buttonWithType:(APButtonType)buttonType title:(NSString *)title target:(id)target action:(SEL)action;

/**

 *  通过指定按钮类型来创建按钮

 *  @param buttonType 按钮类型，必须是定义在 APButtonType 中的其中一个值

 *  @return 新创建并经过初始化的按钮对象

 */
- (id)initWithButtonType:(APButtonType)buttonType;
```

示例：
```C
APButton *confirmBtn = [APButton buttonWithType:APButtonTypeDefault title:confirmButtonTitle target:self action:@selector(onConfirm:)];

APButton *cancelBtn = [APButton buttonWithType:APButtonTypeSecondary title:cancelButtonTitle target:self action:@selector(onCancel:)];
```

##  APCheckBox
类名： APCheckBox 继承 UIControl

描述：该控件表明一个特定的状态（即选项）是选定 (on，值为 true) 还是清除 (off，值为 false)。
接口：

```C
/**
 *  Returns an initialized <code>APCheckBox</code> object with the height of 20.0.
 *  @return An initialized check box object.
 */
- (id)init;
```

属性：

```C
/**
 *  A boolean value indicates whether the <code>APCheckBox</code> is checked.
 *
 *  The default value is NO.
 */
@property(nonatomic, assign, getter = isChecked) BOOL checked;

/** Gets or sets the image for check state. */
@property(nonatomic, retain) UIImage *checkedImage;

/** Gets or sets the image for unchecked state. */
@property(nonatomic, retain) UIImage *uncheckedImage;

/**
 *  The inset or outset margins for the rectangle around the check-box's image.
 *  The default value is <code>UIEdgeInsetsMake(0, 0, 0, 5.0)<code>.
 */
@property(nonatomic, assign) UIEdgeInsets imageEdgeInsets;

/** Returns the label used for title of check box. */
@property(nonatomic, strong, readonly) UILabel *titleLabel;
```
示例：

```C
APCheckBox *checkBox = [[APCheckBox alloc] initWithFrame:CGRectZero];
[checkBox sizeToFit];
[self addSubview:checkBox];
```

##  APCircleRefreshControl
类名： APCircleRefreshControl，继承 UIControl

描述：圆形加载刷新视图

接口：
```C
// 创建视图在指定的 UIScrollView 上
- (id)initInScrollView:(UIScrollView *)scrollView;

// 开始刷新动画
- (void)beginRefreshing;

// 结束刷新动画
- (void)endRefreshing;
```
示例：

```C
self.refreshControl = [[APCircleRefreshControl alloc] initInScrollView:self.tableView];
[self.refreshControl addTarget:self action:@selector(refreshEventRecieved:) forControlEvents:UIControlEventValueChanged];
```

##  APCreditCardTextField
类名： APCreditCardTextField，继承 APTextField

描述：信用卡文本输入框

接口：参考 APTextField

示例：

```C
_textField = [[APCreditCardTextField alloc] initWithFrame:UIEdgeInsetsInsetRect(self.bounds, UIEdgeInsetsMake(0, 0, 1, 0))];
_textField.validator = [APTextValidator creditCardValidator];
```

##  APExceptionView
类名： APExceptionView，继承 UIView

描述：网络异常界面。

接口：

```C
/**

  * 初始化异常view 并设定异常类型

  *  @param frame view 的坐标，必选

  *  @param type  异常类型，必选

  *  @return APExceptionView

*/

- (id)initWithFrame:(CGRect)frame exceptionType:(APExceptionEnum) type;

/**
*获取大图标的只读 UIImageView
*@return UIImageView
*/

- (UIImageView *)getIconImageView;

/**
*获取说明文案的只读 UILabel
*@return UILabel
*/

- (UILabel *)getInfoLabel;

/**
*获取行动按钮 *@return UIButton
*/

- (UIButton *)getActionButton;
```

属性：

```C
typedef enum {
APExceptionEnumNetworkError,
APExceptionEnumEmpty,
APExceptionEnumAlert
} APExceptionEnum;
```
示例：

```C
APExceptionView *exceptionView = APExceptionView alloc initWithFrame:frame exceptionType:APExceptionEnumNetworkError];
```

![screenshot1392628034406](https://t.alipayobjects.com/images/rmsweb/T1KotfXg8fXXXXXXXX.png)

![screenshot1392627993566](https://t.alipayobjects.com/images/rmsweb/T13.4fXktXXXXXXXXX.png)

![screenshot1392628053128](https://t.alipayobjects.com/images/rmsweb/T15E0fXjtaXXXXXXXX.png)

##  APImageAuthCodeBox
类名： APImageAuthCodeBox，继承 APAuthCodeBox

描述：图片验证码输入框

接口：
```C
/**
 *  创建图片证码输入框
 *  @param originY  组件的 y 坐标
 *  @return         图片证码输入框
 */
- (APImageAuthCodeBox *)initWithOriginY:(CGFloat)originY;
```

属性：

```C
@property(strong, nonatomic) UIImageView *authCodeImageView;  // 图片验证码
@property(strong, nonatomic) UIImageView *circleImageView;    // 图片验证码旁边转动的图标
```

示例：

```C
- (void)viewDidLoad
{
    APImageAuthCodeBox *imageBox = [[APImageAuthCodeBox alloc] initWithOriginY:10];
    imageBox.inputBox.textField.placeholder = @"右侧验证码";
    imageBox.authCodeImageView.image = [UIImage imageNamed:@"auth_code_image"]; 
    [self.view addSubview:imageBox];
}
- (void)refrehAuthImage
{
    [imageBox startWaiting];
    [self performSelector:@selector(authImageReady) withObject:nil afterDelay:3];
}
- (void)authImageReady
{
    [imageBox stopWaiting];
}
```

##  APInputBox
类名： APInputBox，继承自 UIView。
描述：它内部包含一个 TextField(可通过 textField 属性获取），也可能包含一个显示在输入框内部右侧的按钮（可通过 iconButton 属性获取)。如果指定了 textFieldFormat，它将对 TextField 的文本自动用空格分组（一般用于证件号码输入）。
创建：
不带右侧按钮
```C
/**
 *  创建输入框组件
 *  @param  originY 输入框的 y 坐标
 *  @param  type 文本输入框的类型
 *  @return 输入框组件
 */
+ (APInputBox *)inputboxWithOriginY:(CGFloat)originY
                       inputboxType:(APInputBoxType)type;
```

带右侧按钮
```C
/**
 *  创建带图标按钮的输入框组件
 *  @param  originY 输入框的 y 坐标
 *  @param  icon 按钮上的图标，44x44
 *  @param  type 文本输入框的类型
 *  @return 带按钮的输入框组件
 */
+ (APInputBox *)inputboxWithOriginY:(CGFloat)originY
                         buttonIcon:(UIImage *)icon
                       inputboxType:(APInputBoxType)type;
```
属性：

```C
APInputBox 内部属性
@property(strong, nonatomic)   APTextField          *textField;                // 内部的 TextField，对 placeHolder 等属性设置
@property(strong, nonatomic)   NSString             *textFieldText             // 此属性来访问 textField 的文本，会自动去除空格
@property(strong, nonatomic)   NSString             *textFieldFormat           // TextField 的分段格式, 如@"#### #### #### ####"，不指定则不分段
@property(strong, nonatomic)   UIButton             *iconButton                // 内部带图标的按钮
@property(assign, nonatomic)   BOOL                 hidesButtonWhileNotEmpty   // 内容为空时是否隐藏按钮   
@property(nonatomic, readonly) UILabel              *titleLabel                // 显示在文本框左边的文本
@property(assign, nonatomic)   APInputBoxStyle      style                      // 文本框的样式，枚举类型
@property(assign, nonatomic)   APInputBoxType       inputBoxType               // 文本框的输入类型，枚举类型
```
示例：
创建带图标按钮的示例代码
```C
APInputBox *phoneNumberBox = [APInputBox inputboxWithOriginY:0 buttonIcon:[UIImage bundledImageNamed:@"input_icon"] inputboxType:APInputBoxTypeMobileNumber];
[phoneNumberBox.iconButton addTarget:self action:@selector(onShowAddressBook) forControlEvents:UIControlEventTouchUpInside];
phoneNumberBox.titleLabel.text = @"手机号码";
phoneNumberBox.textField.placeholder = @"联系人手机号";
phoneNumberBox.hidesButtonWhileNotEmpty = YES;
```
创建不带图标按钮、按手机号码自动分段的示例代码
```C
APInputBox *phoneNumberBox = [APInputBox inputboxWithOriginY:0 inputboxType:APInputBoxTypeMobileNumber];
phoneNumberBox.titleLabel.text = @"手机号码";
phoneNumberBox.textFieldFormat = @"### #### ####";
phoneNumberBox.textField.placeholder = @"联系人手机号";
```
方法：
方法说明
```C
/**
 *  按照指定格式对文本添加空格
 *  @param  文本内容
 *  @return 添加空格后的文本
 */
- (NSString *)formatText:(NSString *)text;

/**
 *  对于没有在初始化时指定 icon 的 inputBox, 可以使用此方法添加
 *  @param icon 按钮上的图标，44x44
 *
 */
- (void)setRightButtonIcon:(UIImage *)icon;

/**
 * 检查输入的有效性.
 */
- (BOOL)checkInputValidity;

/**
 * 过滤文本，只可输入数字，限定最大长度
 * 参数为相应 delegate 参数，maxLength 为最大长度
 */
- (BOOL)shouldChangeRange:(NSRange)range
        replacementString:(NSString *)string
            withMaxLength:(int)maxLength;

/**
 * 限定最大长度
 * @maxLength 最大长度，不包括 format 的空格
 */
- (BOOL)shouldChangeRange:(NSRange)range
        replacementString:(NSString *)string
withFormatStringMaxLength:(int)maxLength;
```

##  APLinkButton
类名： APLinkButton，继承 UIControl

描述：超链接风格的按钮

接口：

没有自定义初始化函数，使用父类定义的初始化函数

```C
/**
 *  设置指定状态时标题显示的颜色
 *  @param color 标题显示的颜色
 *  @param state 指定的状态
 */
- (void)setTitleColor:(UIColor *)color forState:(UIControlState)state;

/**
 *  获取指定状态时标题显示的颜色
 *  @param state 指定的状态
 *  @return 标题显示的颜色
 */
- (UIColor *)titleColorForState:(UIControlState)state;
```

属性：

```C
/** 链接按钮显示标题的视图 */
@property(nonatomic, readonly) UILabel *titleLabel;

/** 链接按钮显示的标题 */
@property(nonatomic, strong) NSString *title;

/** 标示标题下是否显示横线 */
@property(nonatomic, assign) BOOL underline;
```

示例：

```C
APLinkButton  *forgotButton = [[APLinkButton alloc] initWithFrame:shadow.frame];
[forgotButton setTitle:@"忘记登录密码？"];
[forgotButton sizeToFit];
```

##  APLoginBox
类名： APLoginBox，继承 UIView

描述：登录框控件

接口：

```C
/**
 *  输入框与密码框
 */
- (APInputBox *)usernameInputBox;
- (APInputBox *)passwordInputBox;

/**
 * 构造函数
 *
 * @param originY 指定 frame.origin.y
 * @param flag 是否带注册按钮
 * @return 返回登录组件
 *
 */
+ (APLoginBox *)loginBoxWithOriginY:(CGFloat)originY registerButton:(BOOL)flag;


#pragma mark -
#pragma mark 信用卡账单邮箱登陆定制方法
/**
 *  设置下拉框提示内容,并且直接显示下拉列表
 *
 *  @param  historyItems    下拉框中提示的内容
 *  @return 调整后需要增加的高度
 */
- (NSInteger)setHistoryItemsAndDisplayHistory:(NSArray *)historyItems;

/**
 *  设置文本框变更,修改通知逻辑
 */
- (void)setupDidBeginEditNotification;
```

属性：

```C
**
 * 帐号输入框
 */
@property(nonatomic, readonly) UITextField *usernameField;
/**
 * 密码输入框
 */
@property(nonatomic, readonly) UITextField *passwordField;
/**
 * 验证码输入框
 */
@property(nonatomic, readonly) APAuthCodeBox *authCodeBox;
/**
 * 忘记密码"链接"
 */
@property(nonatomic, readonly) APLinkButton *forgotButton;
/**
 * 注册按钮
 */
@property(nonatomic, readonly) UIButton *registerButton;
/**
 * 登录按钮
 */
@property(nonatomic, readonly) UIButton *loginButton;

/**
 * 历史登录账号
 *
 * 数组，每个元素为一个 NSString，将显示在历史帐号列表上
 */
@property(nonatomic, copy) NSArray *historyItems;
/**
 * 历史账号列表是否可见
 */
@property(nonatomic, assign) BOOL historyTableVisible;
/**
 * 验证码控件是否可见
 */
@property(nonatomic, assign) BOOL authCodeBoxVisible;
/**
 * 代理
 */
@property(nonatomic, assign) id<APLoginBoxDelegate> delegate;
```

示例：

```C
_loginBox = [ALPLoginBox loginBoxWithOriginY:90 registerButton:YES];
_loginBox.historyItems = @[@"13127995722", @"mozart0@tom.com", @"hbhftgf@gmail.com"];
_loginBox.delegate = self;
[_loginBox setLoginBoxOriginXCommonPix];
[self.view addSubview:_loginBox];
```

## APMobileTextField
类名： APMobileTextField，继承 APTextField

描述：手机号输入框控件

接口：查看 APTextField

示例：

```C
_textField = [[APMobileTextField alloc] initWithFrame:UIEdgeInsetsInsetRect(self.bounds, UIEdgeInsetsMake(0, 0, 1, 0))];
_textField.validator = [APTextValidator mobileNumberValidator];
```

## APNetErrorView
类名： APNetErrorView，继承 UIView

描述：网络错误提示

接口：
```C
/**
 *  网络异常老控件样式，建议使用新控件 APExceptionView
 *  @param parent   显示在指定父视图
 *  @param target   响应按钮点击事件的对象
 *  @param action   响应按钮点击事件的函数
 *  @return 新创建并经过初始化视图对象。
 */
+ (id)showInParentView:(UIView *)parent withTarget:(id)target action:(SEL)action;

/**
 *  取消显示
 */
- (void)dismiss;

/**
 *  设置指定点击处理
 *  @param target   响应按钮点击事件的对象
 *  @param action   响应按钮点击事件的函数
 */
- (void)setTarget:(id)target action:(SEL)action;
```
示例：
```C
self.netErrorView = [APNetErrorView showInParentView:self.mainView withTarget:self action:@selector(reloadFromRPCByNetErrorRetry)];
```
![screenshot1392628585922](https://t.alipayobjects.com/images/rmsweb/T1o.JfXcleXXXXXXXX.png)

## APNextPagePullView
类名： APNextPagePullView，继承 UIView

描述：实现点击更多出现旋转框，加载下一页成功，旋转框消失。

接口：
```C
/**
  * 视图已经发生滚动的方法
*/
- (void)refreshScrollViewDidScroll:(UIScrollView *)scrollView;

/**
  * 视图已经发生拖动的方法
*/
- (void)refreshScrollViewDidEndDragging:(UIScrollView *)scrollView;

/**
  *数据加载完成
*/
- (void)refreshScrollViewDataSourceDidFinishedLoading:(UIScrollView *)scrollView;

/**
  *数据加载完成后延迟一段时间旋转框消失
- (void)refreshScrollViewDataSourceDidFinishedLoadingAndDelay:(UIScrollView *)scrollView;
```
属性：

```C
@property(nonatomic, weak) id delegate;


@protocol APNextPagePullViewDelegate 
/**
  *判断是否正在加载下一页
*/
- (BOOL)isTableViewLoadingNextPage:(APNextPagePullView *)pullView;

/**
  *视图需要更新的通知
*/
- (void)upPullTableDidTriggerRefresh:(APNextPagePullView *)pullView;
```C
示例：
```C
APNextPagePullView *view = [[APNextPagePullView alloc] initWithFrame:frame];
```

## APNumericKeyboard
类名： APNumericKeyboard，继承 UIView

描述：通过以下静态方法提供一个数字键盘。键盘的确定按钮被点击时，textField 的 delegate 将收到 textFieldShouldReturn: 消息

接口：
```C
+ (APNumericKeyboard *)sharedKeyboard;
```
属性：
```C
//手动设置 textinput，外部需要设置 keyboard 的 y 轴
@property (nonatomic, strong) id<UITextInput> textInput;
//是否是身份证输入键盘
@property (nonatomic, assign, getter = isIdNumber) BOOL idNumber;
//是否隐藏小数点按钮
@property (nonatomic, assign, getter = isDotHidden) BOOL dotHidden;
//是否隐藏收键盘按钮
@property (nonatomic, assign, getter = isDismissHidden) BOOL dismissHidden;
//是否隐藏确定按钮
@property (nonatomic, assign, getter = isSubmitEnable) BOOL submitEnable;
```
示例：
```C
APInputBox *inputBox1 = [APInputBox inputBoxWithOriginY:15 placeHolder:@"身份证"];
inputBox1.textField.inputView = [ALPNumericKeyboard sharedKeyboard];
[self.view addSubview:inputBox1];
```

## APNumPwdInputView
类名： APNumPwdInputView，继承 UIView

描述： 6 位数字密码输入框控件

接口：

```C
/**
 * 初始化 6 位数字密码
 * quadWidth:  单个方格宽度
 * quadHeight: 单个方格高度
 */
- (id)initWithQuadWidth:(CGFloat)quadWidth quadHeight:(CGFloat)quadHeight;

/**
 * 初始化 6 位数字密码
 *
 * quadWidth:  单个方格宽度
 * quadHeight: 单个方格高度
 * password:   初始密码
 */
- (id)initWithQuadWidth:(CGFloat)quadWidth quadHeight:(CGFloat)quadHeight password:(NSString*)password;

/**
 * 清空密码框，不会触发delegate 回调
 */
- (void)reset;

/**
 * 显示键盘
 */
- (BOOL)showKeyBoard;

/**
 * 隐藏键盘
 */
- (BOOL)hideKeyBoard;

/**
 * 设置背景图片
 */
- (void)setBackgroundImage:(UIImage *)image;
```

属性：

```C
/** 用户输入的密码 */
@property (nonatomic, strong, readonly) NSString *password;

/** 
 * APNumPwdInputViewDelegate 代理，代理可以实现密码修改回调函数：
 * - (void)onPasswordDidChange:(APNumPwdInputView*)sender;
 */
@property (nonatomic, assign) id<APNumPwdInputViewDelegate> delegate;

/**
 * 是否是 6 位数字密码控件。如果是 NO，则显示为普通的密码输入框。
 */
@property (nonatomic, assign, getter = isNumericPassword) BOOL numericPassword;
```

示例：

```C
APNumPwdInputView *pwdInputView = [[APNumPwdInputView alloc] initWithQuadWidth:49 quadHeight:44];
pwdInputView.delegate = self;
[self.view addSubview:pwdInputView];
```

## APNumPwdPopupView
类名： APNumPwdPopupView，继承 UIView

描述：

一个弹出框，用于显示 6 位数字密码控件，还有以下元素：

返回按钮（可选 ）
标题
副标题（可选）
取消按钮（可选 ）
确定按钮
接口：

```C
/** 创建弹出框，用于显示 6 位数字密码控件
 * title              标题
 * cancelButtonTitle  取消按钮标题
 * confirmButtonTitle 确定按钮标题
*/
- (id)initWithTitle:(NSString*)title
  cancelButtonTitle:(NSString*)cancelButtonTitle
 confirmButtonTitle:(NSString*)confirmButtonTitle;

/** 创建弹出框，用于显示 6 位数字密码控件
 * title              标题
 * subtitleView       副标题
 * cancelButtonTitle  取消按钮标题
 * confirmButtonTitle 确定按钮标题
*/
- (id)initWithTitle:(NSString*)title
       subtitleView:(UIView *)subtitleView
  cancelButtonTitle:(NSString*)cancelButtonTitle
 confirmButtonTitle:(NSString*)confirmButtonTitle;

/** 如果没有设置 target 或者 selector，执行默认行为 */
- (void)addLeftBackButtonTarget:(id)target selector:(SEL)selector events:(UIControlEvents)events;

/** 获取subtitle 的固定宽度 */
+ (CGFloat )subtitleViewWidth;

/**
 *自定义的 view，用于显示副标题。
 *这个 view 中可以显示任何业务需要自定义的副标题，在调用这个函数之前，subtitleView 应该布局好，并且要计算好它的宽度和高度。
 *如果设置了副标题，在主标题和副标题之间会显示一条分割线。
 */
- (void)showSubtitleView:(UIView *)subtitleView;

/*
 和 UIAlertView 不同，按钮点击后不会自动 dismiss，需要在 apNumPwdPopupViewCallback 回调里手动调用。
 */
- (void)dismiss;
- (void)dismissWithCompletionBlock:(void (^)(void))block;
- (void)clearBlock;
```
示例：

```C
- (void)showALPNumPwdInputView:(NSInteger)tag {

    _pwdPopup = [[APNumPwdPopupView alloc] initWithTitle:@"输入手机支付密码" cancelButtonTitle:@"取消" confirmButtonTitle:@"确定"];

    self.alertViewTag = tag;

    __weak ALPNumPwdPopupView * weekpop = _pwdPopup;
    __weak SWPhonePasswordViewController * weekvc = self;

    _pwdPopup.cancelBlock = ^(ALPNumPwdPopupView * sender, int buttonIndex) {
        [weekpop dismiss];
        [weekvc refreshData];
    };

    _pwdPopup.confirmBlock = ^(ALPNumPwdPopupView * sender, int buttonIndex) {
        [MBProgressHUD showHUDAddedTo:weekpop animated:YES];
        [weekvc rpcForModifyGesture:sender.pwdInputView.password];
    };

    [_pwdPopup presentWithinWindow:DTContextGet().window];
}
```

## APPickerView
类名： APPickerView，继承 UIView

描述：封装 UIPickerView，带有“取消”和“完成”按钮的组合控件

接口：
```C
/*
 * 创建组件
 * @param title 标题，可为 nil
 * @return 创建的组件，默认不显示，需调用 show
 */
+ (APPickerView *)pickerViewWithTitle:(NSString *)title;

/*
 * 初始化对象
 * @param frame 显示位置
 * @param title 显示标题，不显示可设 nil
 * @return 默认返回对象不显示，要显示需要调 show
 */
- (id)initWithFrame:(CGRect)frame withTitle:(NSString *)title;

/*
 * 显示
 */
- (void)show;

/*
 * 隐藏
 */
- (void)hide;

/**
 * 重载数据
 */
- (void)reload;
```
属性：
```C
/** UIPickerView 控件，用于设置它的 dataSource 和 delegate 属性 */
@property(nonatomic, strong) UIPickerView *pickerView;

/**
 *设置 APPickerDelegate 代理，代理必须实现代理函数：
 * 点取消息时回调
 *- (void)cancelPickerView:(APPickerView *)pickerView;
 * 点完成时回调，选中项可通过 pickerView selectedRowInComponent 返回
 *- (void)selectedPickerView:(APPickerView *)pickerView;
*/
@property(nonatomic, weak) id<APPickerDelegate> delegate;
```
示例：
```C
_pickView = [APPickerView pickerViewWithTitle:nil];
_pickView.delegate = self;
_pickView.pickerView.dataSource = self;
[self.view addSubview:_pickView];
// 设置选中位置
[_pickView.pickerView selectRow:0 inComponent:0 animated:YES];
[_pickView show];
```
展示：

![picker](https://t.alipayobjects.com/images/rmsweb/T1AoBfXgpeXXXXXXXX.png)

## APSegmentedControl
类名： APSegmentedControl，继承 UISegmentedControl

描述：指定了分段控件 frame 为 CGRectMake(0, 0, 320.0, 44.0）

接口：

```C
// 创建并初始化内部 frame 为 CGRectMake(0, 0, 320.0, 44.0）
- (id)initWithItems:(NSArray *)items;
```

## APSmsAuthCodeBox
类名： APSmsAuthCodeBox，继承 APAuthCodeBox

描述：短信验证码输入框

接口：

```C
/**
 *  创建短信验证码输入框
 *  @param originY   组件的 y 坐标
 *  @param interval  发送短信前的等待时间
 *  @return          短信验证码输入框
 */
- (APSmsAuthCodeBox *)initWithOriginY:(CGFloat)originY
                             Interval:(NSTimeInterval)interval;
```

属性：
```C
// 有效时间
@property (assign, nonatomic) NSTimeInterval interval;
// 开始时间，在计时，内部会使用当前时间为属性赋值
// 所以在使用短信验证控件时这个属性并不使用
@property (assign, nonatomic) NSTimeInterval startTime;
```
示例：

```C
CGRect frame = CGRectMake(10, originY, 300, 49);
APSmsAuthCodeBox *box = [[APSmsAuthCodeBox alloc] initWithFrame:frame];
box.interval = interval;
box.inputBox.textField.keyboardType = UIKeyboardTypeDecimalPad;
box.inputBox.textField.placeholder = @"短信校验码";
```

## APTextField
类名： APTextField，继承 UITextField

描述：输入框控件，增加了 APTextValidator 验证器的功能。

接口：

```C
/**
 *  创建输入框组件
 *  @param style 文本框样式
 *  @param originY 输入框的 y 坐标
 *  @return 返回输入框组件
 */
+ (APTextField *)textFieldWithStyle:(APTextFieldStyle)style originY:(CGFloat)originY;

/**
 *  Initializes and returns an newly created text field object with the height of 43.0.
 *
 *  @return An initialized text field object.
 */
- (id)init;

/**
 * 检查输入的有效性
 * @return 返回验证结果
 */
- (BOOL)checkInputValidity;

/**
 * 格式化文本
 * @return 返回格式化后的文本
 */
- (NSString *)formatText:(NSString *)text;

/**
 * 格式化之前的原始文本
 * @param textField 文本框
 * @return 返回格式化之前的文本
 */
- (NSString *)getOriginTextWithFormatText:(NSString *)formatText;
```
属性：

```C
@property(strong, nonatomic)   APTextValidator          *validaotr;                //验证器
@property(strong, nonatomic)   NSString             *normalizedText             //目前无用，以后拓展。
@property(nonatomic, strong)   APTextFieldDelegateProxy             *delegateProxy                // 代理 
//以下两个属性不在推荐使用，因为身份证建议使用 APNumericKeyboard
@property(strong, nonatomic)   UIButton             *buttonX                // 用于显示身份证 x 的键
@property(assign, nonatomic)   BOOL                 keyboardTypeIDCard   // 是否是 id 卡


typedef NS_ENUM(NSInteger,APTextFieldStyle){ 
       APTextFieldStylePlain,
      APTextFieldStyleBordered,
};
typedef NS_ENUM(NSInteger,APKeyboardType){
      APKeyboardTypeIDCard = 100,
}```

示例：

```C
_textField = [[APTextField alloc] initWithFrame:UIEdgeInsetsInsetRect(self.bounds, UIEdgeInsetsMake(0, 0, 1, 0))];
_textField.validator = [APTextValidator mobileNumberValidator];
```

## APTipView
类名： APTipView，继承 UIView

描述：提示视图

接口：
```C
/**
 *  创建一个有点击按钮的提示视图
 *  @param frame 在父视图中的位置和大小
 *  @param tip   提示信息
 *  @param title 按钮标题
 *  @return 有点击按钮的提示视图对象
 */
- (id)initWithFrame:(CGRect)frame tipMessage:(NSString *)tip title:(NSString *)title;

/**
 *  创建提示视图
 *  @param frame 在父视图中的位置和大小
 *  @param tip   提示信息
 *  @return 提示视图对象
 */
- (id)initWithFrame:(CGRect)frame tipMessage:(NSString *)tip;
```
属性：
```C
/** APTipViewDelegate 代理，必须实现点击按钮代理方法
 * - (void)tipButtonClick:(UIButton *)sender;
 */
@property (nonatomic, weak) id <APTipViewDelegate> delegate;
```
示例：
```C
self.tipView = [[APTipView alloc] initWithFrame:self.view.frame tipMessage:[NSString stringWithFormat:@"暂无%@，请稍后再试", self.title]];
[self.view addSubview:self.tipView];
```
展示： 

![IMG_1079](https://t.alipayobjects.com/images/rmsweb/T12.RfXoNbXXXXXXXX.png)

## APToastView
类名： APToastView，继承 UIView

描述：提示信息

接口：
```C
/**
 * 显示 Toast
 *
 * @param superview 要在其中显示 toast 的视图
 * @param icon 图标类型, 支持以下几种
 *   APToastIconNone 无图标
 *   APToastIconSuccess 成功图标
 *   APToastIconFailure 失败图标
 * @param text 显示文本
 * @param duration 显示时长
 *
 */
+ (void)presentToastWithin:(UIView *)superview withIcon:(APToastIcon)icon text:(NSString *)text duration:(NSTimeInterval)duration;

/**
 * @param superview 要在其中显示 toast 的视图
 * @param text 显示文本
 * @return 返回显示的 toast 对象
 */
+ (APToastView *)presentToastWithin:(UIView *)superview text:(NSString *)text;

/*
 * 模态显示提示，此时屏幕不响应用户操作
 */
+ (APToastView *)presentToastWithText:(NSString *)text;

/*
 * 模态toast
 * @param superview 父视图
 */
+ (APToastView *)presentModelToastWithin:(UIView *)superview text:(NSString *)text;

/*
 * 使 toast 消失
 */
- (void)dismissToast;

/**
 * 显示 Toast
 *
 * @param superview 要在其中显示 toast 的视图
 * @param icon 图标类型, 支持以下几种
 *   APToastIconNone 无图标
 *   APToastIconSuccess 成功图标
 *   APToastIconFailure 失败图标
 * @param text 显示文本
 * @param duration 显示时长
 * @param completion Toast 自动消失后的回调
 *
 */
+ (void)presentToastWithin:(UIView *)superview withIcon:(APToastIcon)icon text:(NSString *)text duration:(NSTimeInterval)duration completion:(void (^)())completion;

/**
 * 显示模态Toast
 *
 * @param superview 要在其中显示 toast 的视图
 * @param icon 图标类型, 支持以下几种
 *   APToastIconNone 无图标
 *   APToastIconSuccess 成功图标
 *   APToastIconFailure 失败图标
 * @param text 显示文本
 * @param duration 显示时长
 * @param completion Toast 自动消失后的回调
 *
 */
+ (void)presentModalToastWithin:(UIView *)superview withIcon:(APToastIcon)icon text:(NSString *)text duration:(NSTimeInterval)duration completion:(void (^)())completion;
```
示例：
```C
[APToastView  presentToastWithin:weakSelf.view withIcon:ALPToastIconNone text:@"该卡暂不能开通快捷支付,请用其他卡" duration:2.0];
```
展示：

![toast](https://t.alipayobjects.com/images/rmsweb/T1GopfXiBiXXXXXXXX.png)

## APRefreshTableHeaderView
描述：基于开源 EGORefreshTableHeaderView 封装，下拉刷新控件。

## APTableViewCell
接口：
```C
/**
 *  构造，派生类调用基类构造
*/
- (id)initWithStyle:(UITableViewCellStyle)style reuseIdentifier:(NSString *)reuseIdentifier;
```
属性：
```C
typedef NS_ENUM(NSInteger, APTableViewCellStyle) {
    /** Simple cell with text label and optional image view. */
    APTableViewCellStyleSimple,
    APTableViewCellStyleSubtitle = UITableViewCellStyleSubtitle,
};

/**
 *  Default UIEdgeInsetsZero. Add additional padding area around content
 */
@property(nonatomic) UIEdgeInsets contentInset;
```

## APTableViewBaseCell
![tableBaseCellAll](https://t.alipayobjects.com/images/rmsweb/T1h_BfXXBwXXXXXXXX.png)

1.1 类名称：
APTableViewBaseCell

1.2 继承关系：
1.3 使用范围:实现了单行 cell 的大多情况下的需求

3. 操作和行为：
3.1 操作接口：

```C
+ (float)cellHeight;

/**
 * 重置所有子控件，reuse 时应调此方法
 */
- (void)reset;

/**
 * 设置头像图标
 */
- (void)setLogoImg:(UIImage *)img;

/**
 *  注意：需要引入 SDWebImage.framework 才能有效！设置头像为指定 url,并设置默认头像,defaultImg 不可为空
 *
 *  @param imgUrl     图片url
 *  @param defaultImg 默认图片UIImage 实例
 */
- (void)setLogoImgWithUrl:(NSString *)imgUrl withDefault:(UIImage *)defaultImg;

/**
 * 设置主标题
 */
- (void)setTitle:(NSString* )title;

/**
 * 设置是否显示展开图标
 */
- (void)setExtendLogo;

/**
 * 设置默认背景色
 */
- (void)setNormalBackground:(UIColor *)normalColor;

/**
 * 设置选中背景色
 */
- (void)setSelectedBackground:(UIColor *)selectedColor;

/**
 * 设置提示信息，对标题信息进行补充，靠右显示，有展开图标则显示于展开图标左边
 */
- (void)setInfo:(NSString *)info;

/**
 * 设置提示信息字体颜色
 */
- (void)setInfoFontColor:(UIColor *)color;

/**
 * 设计提示图标，位置同上，提示信息和提示图标同时只能设一个
 */
- (void)setInfoImg:(UIImage *)img;

/**
 * 设置开关
 * @prama isOpen 默认开关设置
 * @prama target 开关事件响应对象
 * @prama selector 开关事件响应方法
 */

- (void)setSwitchWithDefault:(BOOL) isOpne withTarget:(id) target withSelector:(SEL) selector;

/**
 * 设置具体详情信息，此类无此效果，richcell 可设置
 */
- (void)setInfoDetail:(NSString *)detail;

- (BOOL)isShowExtend;

- (void)addView:(UIView *)subView;

- (void)enableLineImageView:(BOOL)yesOrNo;

- (NSInteger )contentViewRightGap;
```

2.2 回调事件：
2.3 其他：
注意：如要使用接口(void)setLogoImgWithUrl:(NSString *)imgUrl withDefault:(UIImage *)defaultImg;  需要引入 SDWebImage.framework 才能有效！设置头像为指定 url,并设置默认头像,defaultImg 不可为空

3. 使用示例：

```C
- (UITableViewCell*) tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    APTableViewBaseCell* cell = [tableView dequeueReusableCellWithIdentifier:@"cell"];

    if (cell) {
        [cell reset];
    }
    else {

            cell = [[APTableViewBaseCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"cell"];
    }

    [cell setTitle:@"支付宝"];
    UIImage *img = [UIImage imageNamed:@"icon_58.png"];
    [cell setInfo:@"alipay"];
    [cell setLogoImg:img];

    return cell;
}
```

## APButtonCell
类名： APButtonCell，继承 UITabbleViewCell

描述：带按钮的表格。

接口：
```C
/**
 * 在表格中添加 button 方法
 */
- (void)addButton:(APButton *)button;


/**
 * 在表格中添加一个制定样式，点击事件的 button
 */
- (APButton *)addButtonWithType:(APButtonType)buttonType title:(NSString *)title target:(id)target action:(SEL)action;
```

示例：
```C
APButtonCell *buttonCell = ??[APButtonCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:reuseIdentifier];
APButton *button = ?[APButton  alloc] initWithButtonType:APButtonTypeCustom];
[button setTitle:@"确定"];
?[buttonCell addButton:button];
[buttonCell addButtonWithType:APButtonTypeCustom title:@"确定" target:self  action:@selector(confirm:)];
```

## APInputBoxCell
类名： APInputBoxCell，继承 UITabbleViewCell

描述：带按钮的表格。

接口：

```C
/**
 *控件高度
 */
+ (float)cellHeight;


/**
 * 返回 cell 中的输入框
 */
- (APInputBox *)textFieldInCell;
```

示例：
```C
APInputBoxCell *inputBoxCell = ??[APInputBox alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:reuseIdentifier];
APInputBox *box = [inputBoxCell textFieldInCell];
```

## APSwitchCell
1. 控件的描述：
1.1 类名称：
APSwitchCell

1.2 继承关系：
1.3 使用范围:用于设置
2. 界面属性
2.1 展现效果：

![screenshot1392626534864](https://t.alipayobjects.com/images/rmsweb/T1fUNfXjBdXXXXXXXX.png)

3. 操作和行为：
3.1 操作接口：

```C
/**
 *  返回 cell 高度
 *
 *  @return 高度值
 */
+ (float)cellHeight;

/**
 *  初始化
 *
 *  @param reuseIdentifier cell 复用 id
 *
 *  @return APSwitchCell
 */
- (id)initWithReuseIdentifier:(NSString *)reuseIdentifier;

/**
 *  UISwitch 控件
 */
@property (nonatomic, readonly) UISwitch *switchControl;
```

3.2 回调事件：
3.3 其他：
4. 使用示例：

```C
APSwitchCell *cell = nil;
identifier = @"switch";
cell = [_tableView dequeueReusableCellWithIdentifier:identifier];
if (!cell) {
    cell = [[APSwitchCell alloc] initWithReuseIdentifier:identifier];
    cell.textLabel.text = @"手势密码";
    cell.selectionStyle = UITableViewCellSelectionStyleNone;
    _tableView.separatorStyle = UITableViewCellSeparatorStyleSingleLine;
}
```

## APTableViewDoubleLineCell
类名： APTableViewDoubleLineCell，继承 APTableViewCell

描述：带两条线的 cell

接口：

```C
/**
 *  APTableViewDoubleLineCell 初始化函数，设置的 Style 为 UITableViewCellStyleDefault
 *  推荐使用该接口初始化
 *  @param reuseIdentifier 重用标记
 *  @return 返回初始化的实例
 */
- (id)initWithReuseIdentifier:(NSString *)reuseIdentifier;

/**
 *  返回 cell 的高度
 *  @return cell 的高度
 */
+ (float)cellHeight;


/**
 *  cell 的重置 */
- (void)reset;
```
示例：

```C
APTableViewDoubleLineCell *cell = nil;
identifier = @"DoubleLineCell";
cell = [tableView  dequeueReusableCellWithIdentifier:identifier];
if (!cell) {
cell = [APTableViewDoubleLineCell alloc initWithReuseIdentifier:identifier];
cell.selectionStyle = UITableViewCellSelectionStyleNone;
}
```

## APTableViewTwoTextCell
1.1 类名称：
APTableViewTwoTextCell

1.2 继承关系：
父类： APTableViewCell

1.3 使用范围：
含有左右两个文字标签的 cell。

两边的标签会根据文字的长度自动左右对齐。

2. 界面属性
2.1 展现效果：

![Screen Shot 2014-02-18 at 4.10.39 PM](https://t.alipayobjects.com/images/rmsweb/T10.hfXo8jXXXXXXXX.png)

4. 使用示例：
```C
cell = [[APTableViewTwoTextCell alloc] 

    initWithStyle:UITableViewCellStyleDefault 

    reuseIdentifier:cellId 

    leftText:@"左边的文字" 

    rightText:@"右边的文字"]];
```

## APTextFieldCell
1.1 类名称：
APTextFieldCell

1.2 使用范围:用于表格单元中的输入框场景

1.3 展现效果：
![screenshot1392627786282](https://t.alipayobjects.com/images/rmsweb/T1ooxfXoxgXXXXXXXX.png)

1.4 接口：

```C
@property(nonatomic, strong, readonly) APTextField *textField;
@property(nonatomic, assign) CGFloat textLabelWidth;
```