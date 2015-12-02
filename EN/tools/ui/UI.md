@cnName 1 UI Controls
@priority 1

# 1 UI Controls

[TOC]

## APActionSheet
Class name:   APActionSheet, inherited from UIActionSheet. 

Description: It defined the ActionSheet base class, and manages ActionSheet in a unified way. 

Interface:

```C
+(void)setIsBackGroundMode:(bool)isBackGroundMode;
```

Property: 

```C
@property(nonatomic,weak)id<UIActionSheetDelegate>dtDelegate;
```

Example: 

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
        // The cancel button is placed at the end. 
        [_contactActionSheet addButtonWithTitle:TRANSFER_TEXT_TO_ACCOUNT_SHEET_CANCEL];
        [_contactActionSheet showInView:self.view];
    }
}
```

##  APActionSheetManager
Class name:  APActionSheetManager, inherited from NSObject. 

Description: It manages APActionSheet. 

Interface:

```C
/**
  * Create a singleton class. 
*/

+ (APActionSheetManager *)sharedAPActionSheetManager;

/**
  * Delete all the actionsheet. 
*/
-(void)dismissAllUIActionSheet;

/**
  * Add actionsheet to the "shown" queue. 
*/
-(void)addToShowedPool:(APActionSheet*)alertView;

````

Property:

```C
@property(nonatomic,assign)bool backGroundMode;
```

Example:

```C
APActionSheetManager *manager = [APActionSheetManager sharedAPActionSheetManager];
```

##  APAgreementBox
Class name:  APAgreementBox, inherited from UIControl. 

Description: It shows the components of user registration agreements. 

Interface:

```C
/**
 *  It shows the components of user registration agreements.
 *  @param frame     The position and size of the view in the parent view. 
 *  @param labelText The title. 
 *  @param linkText  The hyper link title. 
 *  @return The initialized registration agreement components newly created. 
 */
- (id)initWithFrame:(CGRect)frame labelText:(NSString *)labelText linkText:(NSString *)linkText;

// Whether to show the check box. 
- (void)setCheckBoxHidden:(BOOL)hidden;
```

Property:

```C
// User checks the check box. 
@property(nonatomic, readonly) APCheckBox *checkBox;
// User clicks the hyperlink button. 
@property(nonatomic, readonly) APLinkButton *linkButton;
```

Example:

```C
APAgreementBox *agreementBox = [APAgreementBox alloc] initWithFrame:15
                                                          labelText:@"Agree"
                                                           linkText:@"Alipay Registration Agreement"];
[self.view addSubview:agreementBox];
```

##  APAlertView
Class name:  APAlertView, inherited from UIAlertView. 

Description:  Customized UIAlertView. When there are only two buttons, the buttons will be arranged along the up-down direction. 

Usage:   Like UIAlertView, use this class when you want the two buttons arranged in the up-down direction. Use UIAlertView in other cases. 

Example:

```C
APAlertView *alertView = [[APAlertView alloc] initWithTitle:@"New alert"
                                                    message:@"We will notify you of new alerts, services and help information related to your account."
                                                   delegate:self
                                          cancelButtonTitle:@"Get it"
                                          otherButtonTitles:@"Issues with use", nil];
[alertView show];
```

##  APAlertViewManager
Class name:  APAlertViewManager, inherited from NSObject. 

Description: It manages APAlertView. 

Interface:
```C
/**

  * Create a singleton class.

*/

+ (APAlertViewManager *)sharedAPAlertViewManager;

/**
  * Delete all alertView. 
*/
- (void)removeAllAlertView;

/**
  * Add alertView to the "shown" queue. 
*/
- (void)pushAPAlertView:(UIAlertView *)alertview;
```
Property:

```C
@property(nonatomic,assign)bool isBackGroundMode;
```

Example:

```C
APAlertViewManager *manager = [APAlertViewManager sharedAPAlertViewManager];
```

##  APBankCardTextField
Class name:  APBankCardTextField, inherited from APTextField. 

Description: It is the text field of the bank card. 

Interafces: Refer to APTextField. 

Example:

```C
APBankCardTextField *textField = [[APBankCardTextField alloc] initWithFrame:UIEdgeInsetsInsetRect(self.bounds, UIEdgeInsetsMake(0, 0, 1, 0))];
textField.validator = [APTextValidator bankCardValidator];
```

##  APBarButtonItem
Class name:  APBarButtonItem, inherited from UIBarButtonItem. 

Description:  APBarButtonItem is the button object placed on the UIToolbar or UINavigationBar. It responds to the user's actions.  

Interface:

```C
/**
 *  @return Crate a new object of the UIBarButtonSystemItemFlexibleSpace class for adjusting the space between buttons. 
 */
+ (APBarButtonItem *)flexibleSpaceItem;

/**
 *  It creates and initializes a button object of "Back". 
 *  @param title    The button title. Note: No effect in settings. The default return image is used internally. 
 *  @param target   The object responding to the button click event. 
 *  @param action   The function responding to the button click event.
 *  @return The initialized "Back" button object newly created.
 */
+ (APBarButtonItem *)backBarButtonItemWithTitle:(NSString *)title target:(id)target action:(SEL)action

/**
 *  It creates and initializes a button object with a specified image.
 *  @param image    Button image. 
 *  @param target   The object responding to the button click event.
 *  @param action   The function responding to the button click event.
 *  @return The newly-created and initialized button object with a specified image.
 */
- (id)initWithImage:(UIImage *)image style:(UIBarButtonItemStyle)style target:(id)target action:(SEL)action

/**
 *  It creates and initializes a button object with a specified title.
 *  @param title    Button title. 
 *  @param target   The object responding to the button click event.
 *  @param action   The function responding to the button click event.
 *  @return The newly-created and initialized button object with a specified title.
 */
- (id)initWithTitle:(NSString *)title style:(UIBarButtonItemStyle)style target:(id)target action:(SEL)action
```
Example:

```C
APBarButtonItem *item = [[APBarButtonItem alloc] initWithImage:[UIImage imageNamed:@"barbutton"] style:0 target:self action:@selector(didReceiveMemoryWarning)];
self.navigationItem.rightBarButtonItem = item;

APBarButtonItem *item = [[APBarButtonItem alloc] initWithTitle:@"Bill" style:0 target:self action:@selector(didReceiveMemoryWarning)]; 
self.navigationItem.rightBarButtonItem = item;
```

##  APBorderedButton
Class name:  APBorderedButton, inherited from UIButton. 

Description: It achieves the three button styles below by default: 

Main button;   
Secondary button; 
Self-defined button. No style will be applied and no initialization of the frame. 

Interface:
```C

/**
 *  An auxiliary method of the method, used for creating and initializing a button object. 
 *
 *  @param buttonType Button type. It must be one of the values defined in <code>APBorderedButtonType</code>. 
 *  @param title      Button title.
 *  @param target     The object responding to the button click event.
 *  @param action      The function responding to the button click event.
 *
 *  @return The initialized button object newly created.
 *
 *  The button object created with this method has a default frame of <code>CGRectMake (0.0, 0, title width+20, 26.0)</code>. 
 *  The events of the specified target and action are <code>UIControlEventTouchUpInside</code> events. 
 */
+ (APBorderedButton *)buttonWithType:(APBorderedButtonType)buttonType
                               title:(NSString *)title
                              target:(id)target
                              action:(SEL)action;

/**
 *  It creates and initializes a button object.
 * 
 *  @param title      Button title.
 *  @param buttonType Button type. It must be one of the values defined in <code>APBorderedButtonType</code>.
 *
 *  @return The initialized button object newly created.
 */
- (id)initWithButtonTitle:(NSString *)title buttonType:(APBorderedButtonType)buttonType;

```
Example:
```C
APBorderedButton *confirmBtn = [APBorderedButton buttonWithType:APBorderedButtonTypeDefault title:confirmButtonTitle target:self action:@selector(onConfirm:)];
```

##  APButton
Class name:  APButton, inherited from UIButton.

Description: It achieves the three button styles below by default:

Main button;  
Secondary button;
Warning button. The button background is usually in the catchy red color. 
Self-defined button. No style will be applied and no initialization of the frame.

Interface:
```C
/**

 *  An auxiliary method of the method, used for creating and initializing a button object.

 *  @param buttonType Button type. It must be one of the values defined in APButtonType.

 *  @param title      Button title.

 *  @param target     The object responding to the button click event.

 *  @param action      The function responding to the button click event.

 *  @return The initialized button object newly created.

 *  The button object created with this method has a default frame of CGRectMake (15.0, 0, 290.0, 42.0).

 *  The events of the specified target and action are UIControlEventTouchUpInside events.

 */
+ (APButton *)buttonWithType:(APButtonType)buttonType title:(NSString *)title target:(id)target action:(SEL)action;

/**

 *  It creates buttons with a specified type. 

 *  @param buttonType Button type. It must be one of the values defined in APButtonType.

 *  @return The initialized button object newly created.

 */
- (id)initWithButtonType:(APButtonType)buttonType;
```

Example:
```C
APButton *confirmBtn = [APButton buttonWithType:APButtonTypeDefault title:confirmButtonTitle target:self action:@selector(onConfirm:)];

APButton *cancelBtn = [APButton buttonWithType:APButtonTypeSecondary title:cancelButtonTitle target:self action:@selector(onCancel:)];
```

##  APCheckBox
Class name:  APCheckBox, inherited from UIControl. 

Description: This control indicates a specific status (option): check (on, the value is "true"), or uncheck (off, the value is "false"). 
Interface:

```C
/**
 *  Returns an initialized <code>APCheckBox</code> object with the height of 20.0.
 *  @return An initialized check box object.
 */
- (id)init;
```

Property:

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
Example:

```C
APCheckBox *checkBox = [[APCheckBox alloc] initWithFrame:CGRectZero];
[checkBox sizeToFit];
[self addSubview:checkBox];
```

##  APCircleRefreshControl
Class name:  APCircleRefreshControl, inherited from UIControl. 

Description: The refresh view with a circle. 

Interface:
```C
// Create views on the specified UIScrollView. 
- (id)initInScrollView:(UIScrollView *)scrollView;

// Start the refreshing animations. 
- (void)beginRefreshing;

// End the refreshing animations.
- (void)endRefreshing;
```
Example:

```C
self.refreshControl = [[APCircleRefreshControl alloc] initInScrollView:self.tableView];
[self.refreshControl addTarget:self action:@selector(refreshEventRecieved:) forControlEvents:UIControlEventValueChanged];
```

##  APCreditCardTextField
Class name:  APCreditCardTextField, inherited from APTextField. 

Description: It is the text field of the credit card.

Interafces: Refer to APTextField.

Example:

```C
_textField = [[APCreditCardTextField alloc] initWithFrame:UIEdgeInsetsInsetRect(self.bounds, UIEdgeInsetsMake(0, 0, 1, 0))];
_textField.validator = [APTextValidator creditCardValidator];
```

##  APExceptionView
Class name:  APExceptionView, inherited from UIView. 

Description: The interface of network exceptions.

Interface:

```C
/**

  * It initializes the exception view and sets the exception type. 

  *  @param frame Coordinates of the view, required. 

  *  @param type  Exception type, required. 

  *  @return APExceptionView.

*/

- (id)initWithFrame:(CGRect)frame exceptionType:(APExceptionEnum) type;

/**
*Get the read-only UIImageView of large icons. 
*@return UIImageView
*/

- (UIImageView *)getIconImageView;

/**
*Get the read-only UILabel description copy. 
*@return UILabel
*/

- (UILabel *)getInfoLabel;

/**
* Get the action button. *@return UIButton
*/

- (UIButton *)getActionButton;
```

Property:

```C
typedef enum {
APExceptionEnumNetworkError,
APExceptionEnumEmpty,
APExceptionEnumAlert
} APExceptionEnum;
```
Example:

```C
APExceptionView *exceptionView = APExceptionView alloc initWithFrame:frame exceptionType:APExceptionEnumNetworkError];
```

![screenshot1392628034406](https://t.alipayobjects.com/images/rmsweb/T1KotfXg8fXXXXXXXX.png)

![screenshot1392627993566](https://t.alipayobjects.com/images/rmsweb/T13.4fXktXXXXXXXXX.png)

![screenshot1392628053128](https://t.alipayobjects.com/images/rmsweb/T15E0fXjtaXXXXXXXX.png)

##  APImageAuthCodeBox
Class name:  APImageAuthCodeBox, inherited from APAuthCodeBox. 

Description: The input field of image verification code. 

Interface:
```C
/**
 *  It creates the input field of image verification code. 
 *  @param originY  The y-coordinate of the component. 
 *  @return         The input field of image verification code.
 */
- (APImageAuthCodeBox *)initWithOriginY:(CGFloat)originY;
```

Property:

```C
@property(strong, nonatomic) UIImageView *authCodeImageView;  // Image verification code.
@property(strong, nonatomic) UIImageView *circleImageView;    // The circling icon next to the image verification code.
```

Example:

```C
- (void)viewDidLoad
{
    APImageAuthCodeBox *imageBox = [[APImageAuthCodeBox alloc] initWithOriginY:10];
    imageBox.inputBox.textField.placeholder = @"Verification code on the right. ";
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
Class name:  APInputBox, inherited from UIView. 
Description: It contains a text field which can be acquired through the textField property, and may contain a button shown on the right side in the input field (The button can be acquired from the iconButton property).  If the textFieldFormat is specified, it will group texts in the text field with spaces (usually used for inputting ID numbers). 
Create: 
with no button on the right side in the input field. 
```C
/**
 *  Create input field components. 
 *  @param originY  The y-coordinate of the input field.
 *  @param  type The type of the text field.
 *  @return The input field components.
 */
+ (APInputBox *)inputboxWithOriginY:(CGFloat)originY
                       inputboxType:(APInputBoxType)type;
```

with a button on the right side in the input field.
```C
/**
 *  Create input field components of buttons with icons. 
 *  @param originY  The y-coordinate of the input field.
 *  @param icon Icons on the button. Size: 44x44.
 *  @param  type The type of the text field.
 *  @return The input field components of buttons with icons. 
 */
+ (APInputBox *)inputboxWithOriginY:(CGFloat)originY
                         buttonIcon:(UIImage *)icon
                       inputboxType:(APInputBoxType)type;
```
Property:

```C
Internal properties of APInputBox
@property(strong, nonatomic)   APTextField          *textField;                // Settings on properties such as placeHolder of internal TextField. 
@property(strong, nonatomic)   NSString             *textFieldText             // This property accesses texts in the textField and removes spaces automatically. 
@property(strong, nonatomic)   NSString             *textFieldFormat           // The segmenting format of TextField texts, such as @"#### #### #### ####". If it is not specified, no segmenting will be done. 
@property(strong, nonatomic)   UIButton             *iconButton                // Internal buttons with icons. 
@property(assign, nonatomic)   BOOL                 hidesButtonWhileNotEmpty   // Whether to hide buttons when contents are empty.    
@property(nonatomic, readonly) UILabel              *titleLabel                // Texts shown on the left side of the text field. 
@property(assign, nonatomic)   APInputBoxStyle      style                      // Input box style, of the enumeration type. 
@property(assign, nonatomic)   APInputBoxType       inputBoxType               // Input box type, of the enumeration type.
```
Example:
*  Create example codes of buttons with icons.
```C
APInputBox *phoneNumberBox = [APInputBox inputboxWithOriginY:0 buttonIcon:[UIImage bundledImageNamed:@"input_icon"] inputboxType:APInputBoxTypeMobileNumber];
[phoneNumberBox.iconButton addTarget:self action:@selector(onShowAddressBook) forControlEvents:UIControlEventTouchUpInside];
phoneNumberBox.titleLabel.text = @"Mobile number";
phoneNumberBox.textField.placeholder = @"Mobile number of contact";
phoneNumberBox.hidesButtonWhileNotEmpty = YES;
```
Create example codes of buttons without icons. The codes are segmented by the mobile number automatically. 
```C
APInputBox *phoneNumberBox = [APInputBox inputboxWithOriginY:0 inputboxType:APInputBoxTypeMobileNumber];
phoneNumberBox.titleLabel.text = @"Mobile number";
phoneNumberBox.textFieldFormat = @"### #### ####";
phoneNumberBox.textField.placeholder = @"Mobile number of contact";
```
Method: 
Description
```C
/**
 *  Add spaces to the text according to a specified format. 
 *  @param  Text content. 
 *  @return Texts after spaces are added. 
 */
- (NSString *)formatText:(NSString *)text;

/**
 *  This method applies to inputBox with no icon specified at the initialization. 
 *  @param icon Icons on the button. Size: 44x44.
 *
 */
- (void)setRightButtonIcon:(UIImage *)icon;

/**
 * Check the input validity. 
 */
- (BOOL)checkInputValidity;

/**
 * Filter texts. Only digits are allowed and the maximum length is limited. 
 * The arguments are the corresponding delegate arguments. The maxLength indicates the maximum length. 
 */
- (BOOL)shouldChangeRange:(NSRange)range
        replacementString:(NSString *)string
            withMaxLength:(int)maxLength;

/**
 * Limit the maximum length. 
 * @maxLength The maximum length, not including format spaces. 
 */
- (BOOL)shouldChangeRange:(NSRange)range
        replacementString:(NSString *)string
withFormatStringMaxLength:(int)maxLength;
```

##  APLinkButton
Class name:  APLinkButton, inherited from UIControl. 

Description: The button of the hyper link style. 

Interface:

No self-defined initialization functions. The initialization functions defined by the parent class are used. 

```C
/**
 *  Set the color of titles in a specified status. 
 *  @param color Color of the title. 
 *  @param state The specified status. 
 */
- (void)setTitleColor:(UIColor *)color forState:(UIControlState)state;

/**
 *  Get the color of titles in a specified status.
 *  @param state The specified status.
 *  @return Color of the title. 
 */
- (UIColor *)titleColorForState:(UIControlState)state;
```

Property:

```C
/** View of the titles of link buttons */
@property(nonatomic, readonly) UILabel *titleLabel;

/** Titles of link buttons */
@property(nonatomic, strong) NSString *title;

/** Whether to underline titles */
@property(nonatomic, assign) BOOL underline;
```

Example:

```C
APLinkButton  *forgotButton = [[APLinkButton alloc] initWithFrame:shadow.frame];
[forgotButton setTitle:@"Forgot password?"] ;
[forgotButton sizeToFit];
```

##  APLoginBox
Class name:  APLoginBox, inherited from UIView. 

Description: Login box control. 

Interface:

```C
/**
 *  Input box and password box. 
 */
- (APInputBox *)usernameInputBox;
- (APInputBox *)passwordInputBox;

/**
 * Constructor
 *
 * @param originY It specifies the frame.origin.y. 
 * @param flag Whether to include the register button. 
 * @return The login components. 
 *
 */
+ (APLoginBox *)loginBoxWithOriginY:(CGFloat)originY registerButton:(BOOL)flag;


#pragma mark -
#pragma mark Customize login to email box of credit card statements 
/**
 *  Set prompt contents in the drop-down box and show the pull-down list directly
 *
 *  @param  historyItems    Prompt contents in the drop-down box. 
 *  @return The increased height after the adjustment. 
 */
- (NSInteger)setHistoryItemsAndDisplayHistory:(NSArray *)historyItems;

/**
 *  Set text field changes and modify the notification logic. 
 */
- (void)setupDidBeginEditNotification;
```

Property:

```C
**
 * Account input box. 
 */
@property(nonatomic, readonly) UITextField *usernameField;
/**
 * Password input box. 
 */
@property(nonatomic, readonly) UITextField *passwordField;
/**
 * Verification code input box.
 */
@property(nonatomic, readonly) APAuthCodeBox *authCodeBox;
/**
 * Forgot password "link". 
 */
@property(nonatomic, readonly) APLinkButton *forgotButton;
/**
 * Register button. 
 */
@property(nonatomic, readonly) UIButton *registerButton;
/**
 * Login button. 
 */
@property(nonatomic, readonly) UIButton *loginButton;

/**
 * History accounts. 
 *
 * An array. Each element is an NSString and will be shown on the history account list. 
 */
@property(nonatomic, copy) NSArray *historyItems;
/**
 * Whether to show history account list. 
 */
@property(nonatomic, assign) BOOL historyTableVisible;
/**
 * Whether to show verification code control. 
 */
@property(nonatomic, assign) BOOL authCodeBoxVisible;
/**
 * Proxy.
 */
@property(nonatomic, assign) id<APLoginBoxDelegate> delegate;
```

Example:

```C
_loginBox = [ALPLoginBox loginBoxWithOriginY:90 registerButton:YES];
_loginBox.historyItems = @[@"13127995722", @"mozart0@tom.com", @"hbhftgf@gmail.com"];
_loginBox.delegate = self;
[_loginBox setLoginBoxOriginXCommonPix];
[self.view addSubview:_loginBox];
```

## APMobileTextField
Class name:  APMobileTextField, inherited from APTextField.

Description: Input box control of the mobile number. 

Interface: See APTextField.

Example:

```C
_textField = [[APMobileTextField alloc] initWithFrame:UIEdgeInsetsInsetRect(self.bounds, UIEdgeInsetsMake(0, 0, 1, 0))];
_textField.validator = [APTextValidator mobileNumberValidator];
```

## APNetErrorView
Class name:  APNetErrorView, inherited from UIView. 

Description: Network error prompt. 

Interface:
```C
/**
 *  The old control style of network exceptions. The new control APExceptionView is recommended for use. 
 *  @param parent   Show in the specified parent view. 
 *  @param target   The object responding to the button click event.
 *  @param action   The function responding to the button click event.
 *  @return The initialized view object newly created.
 */
+ (id)showInParentView:(UIView *)parent withTarget:(id)target action:(SEL)action;

/**
 *  Cancel view. 
 */
- (void)dismiss;

/**
 *  Specify actions to clicks. 
 *  @param target   The object responding to the button click event.
 *  @param action   The function responding to the button click event.
 */
- (void)setTarget:(id)target action:(SEL)action;
```
Example:
```C
self.netErrorView = [APNetErrorView showInParentView:self.mainView withTarget:self action:@selector(reloadFromRPCByNetErrorRetry)];
```
![screenshot1392628585922](https://t.alipayobjects.com/images/rmsweb/T1o.JfXcleXXXXXXXX.png)

## APNextPagePullView
Class name:  APNextPagePullView, inherited from UIView. 

Description: Show a circling box when the user clicks on "More". Once the next page is loaded, the circling box disappears. 

Interface:
```C
/**

  * The method that scroll did occur in the view. 

*/

- (void)refreshScrollViewDidScroll:(UIScrollView *)scrollView;

/**
  * The method that drag did occur in the view.
*/
- (void)refreshScrollViewDidEndDragging:(UIScrollView *)scrollView;

/**
  *Data loading complete. 
*/
- (void)refreshScrollViewDataSourceDidFinishedLoading:(UIScrollView *)scrollView;

/**
  * After data loading is complete, the circling box disappears after a while. 
- (void)refreshScrollViewDataSourceDidFinishedLoadingAndDelay:(UIScrollView *)scrollView;
```
Property:

```C
@property(nonatomic, weak) id delegate;


@protocol APNextPagePullViewDelegate 
/**
  * Judge whether the next page is being loaded. 
*/
- (BOOL)isTableViewLoadingNextPage:(APNextPagePullView *)pullView;

/**
  * Notification that requires view refreshing. 
*/
- (void)upPullTableDidTriggerRefresh:(APNextPagePullView *)pullView;
```C
Example:
```C
APNextPagePullView *view = [[APNextPagePullView alloc] initWithFrame:frame];
```

## APNumericKeyboard
Class name:  APNumericKeyboard, inherited from UIView. 

Description: It offers a numeric keyboard with the following static methods.  When the OK button of the keyboard is clicked, the delegate of the textField will receive the textFieldShouldReturn:  Message

Interface:
```C
+ (APNumericKeyboard *)sharedKeyboard;
```
Property:
```C
//Set textinput manually. The y-axis of the keyboard should be set externally. 
@property (nonatomic, strong) id<UITextInput> textInput;
//Whether the keyboard is used for entering the ID number. 
@property (nonatomic, assign, getter = isIdNumber) BOOL idNumber;
//Whether to hide the decimal point. 
@property (nonatomic, assign, getter = isDotHidden) BOOL dotHidden;
//Whether to hide the button for tucking the keyboard. 
@property (nonatomic, assign, getter = isDismissHidden) BOOL dismissHidden;
//Whether to hide the OK button. 
@property (nonatomic, assign, getter = isSubmitEnable) BOOL submitEnable;
```
Example:
```C
APInputBox *inputBox1 = [APInputBox inputBoxWithOriginY:15 placeHolder:@"ID card"];
inputBox1.textField.inputView = [ALPNumericKeyboard sharedKeyboard];
[self.view addSubview:inputBox1];
```

## APNumPwdInputView
Class name:  APNumPwdInputView, inherited from UIView. 

Description:  The input box control of 6-digit password. 

Interface:

```C
/**
 * Initialize the 6-digit password. 
 * quadWidth:   Width of quad.
 * quadHeight:  Height of quad.
 */
- (id)initWithQuadWidth:(CGFloat)quadWidth quadHeight:(CGFloat)quadHeight;

/**
 * Initialize the 6-digit password.
 *
 * quadWidth:   Width of quad. 
 * quadHeight:  Height of quad.
 * password:    Initial password. 
 */
- (id)initWithQuadWidth:(CGFloat)quadWidth quadHeight:(CGFloat)quadHeight password:(NSString*)password;

/**
 * Clear the password box, which will not trigger the callback of the delegate. 
 */
- (void)reset;

/**
 * Show the keyboard. 
 */
- (BOOL)showKeyBoard;

/**
 * Hide the keyboard. 
 */
- (BOOL)hideKeyBoard;

/**
 * Set the background image. 
 */
- (void)setBackgroundImage:(UIImage *)image;
```

Property:

```C
/** User entered password */
@property (nonatomic, strong, readonly) NSString *password;

/** 
 * APNumPwdInputViewDelegate. The delegate can implement the callback function for password changing: 
 * - (void)onPasswordDidChange:(APNumPwdInputView*)sender;
 */
@property (nonatomic, assign) id<APNumPwdInputViewDelegate> delegate;

/**
 * Judge whether it is a 6-digit password control.  If NO, the general password input box will show. 
 */
@property (nonatomic, assign, getter = isNumericPassword) BOOL numericPassword;
```

Example:

```C
APNumPwdInputView *pwdInputView = [[APNumPwdInputView alloc] initWithQuadWidth:49 quadHeight:44];
pwdInputView.delegate = self;
[self.view addSubview:pwdInputView];
```

## APNumPwdPopupView
Class name:  APNumPwdPopupView, inherited from UIView. 

Description:

A pop-up box to show the 6-digit password control, as well as the following elements: 

Back button (optional)
Title
Subtitle (optional)
Cancel button (optional)
OK button
Interface:

```C
/** Create a pop-up box to show the 6-digit password control. 
 * title              Title
 * cancelButtonTitle  Cancel the button title. 
 * confirmButtonTitle Confirm the button title. 
*/
- (id)initWithTitle:(NSString*)title
  cancelButtonTitle:(NSString*)cancelButtonTitle
 confirmButtonTitle:(NSString*)confirmButtonTitle;

/** Create a pop-up box to show the 6-digit password control.
 * title              Title
 * subtitleView       Subtitle
 * cancelButtonTitle  Cancel the button title.
 * confirmButtonTitle Confirm the button title.
*/
- (id)initWithTitle:(NSString*)title
       subtitleView:(UIView *)subtitleView
  cancelButtonTitle:(NSString*)cancelButtonTitle
 confirmButtonTitle:(NSString*)confirmButtonTitle;

/** If no target or selector is set, the default execution action is */
- (void)addLeftBackButtonTarget:(id)target selector:(SEL)selector events:(UIControlEvents)events;

/** Get the fixed width of subtitle */
+ (CGFloat )subtitleViewWidth;

/**
 *Self-defined view to show the subtitle. 
 *This view will show the subtitles that any business needs to define by itself. Before this function is called, the subtitleView layout should be ready with its width and height calculated. 
 *If the subtitle has been set, a divider line will be shown between the title and the subtitle. 
 */
- (void)showSubtitleView:(UIView *)subtitleView;

/*
 Unlike UIAlertView, no automatic dismissing will be conducted after button clicks. You need to call dismissing manually in apNumPwdPopupViewCallback. 
 */
- (void)dismiss;
- (void)dismissWithCompletionBlock:(void (^)(void))block;
- (void)clearBlock;
```
Example:

```C
- (void)showALPNumPwdInputView:(NSInteger)tag {

    _pwdPopup = [[APNumPwdPopupView alloc] initWithTitle:@"Enter password for payment on the phone" cancelButtonTitle:@"Cancel" confirmButtonTitle:@"OK"];

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
Class name:  APPickerView, inherited from UIView.

Description: It encapsulates compound widgets with "Cancel" and "Complete" buttons on the UIPickerView widget. 

Interface:
```C
/*
 * Create components. 
 * @param title Title. Its value can be nil. 
 * @return The created component, not shown by default. The show method needs to be called to show it. 
 */
+ (APPickerView *)pickerViewWithTitle:(NSString *)title;

/*
 * Initialize objects. 
 * @param frame Display position. 
 * @param title Show the title. Set the value to nil if you do not want to show the title. 
 * @return Do not show the object by defaut. To show the object, call the show method. 
 */
- (id)initWithFrame:(CGRect)frame withTitle:(NSString *)title;

/*
 * Show
 */
- (void)show;

/*
 * Hide
 */
- (void)hide;

/**
 * Reload data. 
 */
- (void)reload;
```
Property:
```C
/** UIPickerView widget. Used to set its dataSource and delegate properties. */
@property(nonatomic, strong) UIPickerView *pickerView;

/**
 *Set APPickerDelegate. The delegate must implement the delegate functions: 
 * Callback when clicking to pick messages. 
 *- (void)cancelPickerView:(APPickerView *)pickerView;
 * Callback when clicking to complete. The selected items can be returned through the pickerView selectedRowInComponent method.
 *- (void)selectedPickerView:(APPickerView *)pickerView;
*/
@property(nonatomic, weak) id<APPickerDelegate> delegate;
```
Example:
```C
_pickView = [APPickerView pickerViewWithTitle:nil];
_pickView.delegate = self;
_pickView.pickerView.dataSource = self;
[self.view addSubview:_pickView];
// Set the selected position. 
[_pickView.pickerView selectRow:0 inComponent:0 animated:YES];
[_pickView show];
```
Demo: 

![picker](https://t.alipayobjects.com/images/rmsweb/T1AoBfXgpeXXXXXXXX.png)

## APSegmentedControl
Class name:  APSegmentedControl, inherited from UISegmentedControl. 

Description: It specifies the segmenting widget frame as CGRectMake (0, 0, 320.0, 44.0). 

Interface:

```C
// Create and initialize the internal frame to CGRectMake (0, 0, 320.0, 44.0). 
- (id)initWithItems:(NSArray *)items;
```

## APSmsAuthCodeBox
Class name:  APSmsAuthCodeBox, inherited from APAuthCodeBox.

Description: The input field of text verification code.

Interface:

```C
/**
 *  Create the input field of text verification code.
 *  @param originY  The y-coordinate of the component.
 *  @param interval  The wait time before the text message is sent. 
 *  @return          The input box of text verification code.
 */
- (APSmsAuthCodeBox *)initWithOriginY:(CGFloat)originY
                             Interval:(NSTimeInterval)interval;
```

Property:
```C
// Valid time. 
@property (assign, nonatomic) NSTimeInterval interval;
// Start time. Internally the current time will be used to assign values to the property. 
// So this property is not used when the text message verification widget is used. 
@property (assign, nonatomic) NSTimeInterval startTime;
```
Example:

```C
CGRect frame = CGRectMake(10, originY, 300, 49);
APSmsAuthCodeBox *box = [[APSmsAuthCodeBox alloc] initWithFrame:frame];
box.interval = interval;
box.inputBox.textField.keyboardType = UIKeyboardTypeDecimalPad;
box.inputBox.textField.placeholder = @"Text verification code.";
```

## APTextField
Class name:  APTextField, inherited from UITextField.

Description: The input box widget which enriches the functions of APTextValidator. 

Interface:

```C
/**
 *  Create input field components.
 *  @param style The text box style. 
 *  @param originY  The y-coordinate of the input field.
 *  @return The input field components.
 */
+ (APTextField *)textFieldWithStyle:(APTextFieldStyle)style originY:(CGFloat)originY;

/**
 *  Initializes and returns an newly created text field object with the height of 43.0.
 *
 *  @return An initialized text field object.
 */
- (id)init;

/**
 * Check the input validity.
 * @return The validation result. 
 */
- (BOOL)checkInputValidity;

/**
 * Format texts. 
 * @return The formatted texts. 
 */
- (NSString *)formatText:(NSString *)text;

/**
 * The original texts before formatting. 
 * @param textField Text box. 
 * @return The texts before formatting.
 */
- (NSString *)getOriginTextWithFormatText:(NSString *)formatText;
```
Property:

```C
@property(strong, nonatomic)   APTextValidator          *validaotr;                //Validator.
@property(strong, nonatomic)   NSString             *normalizedText             //Not used at the moment. Will be extended in the future. 
@property(nonatomic, strong)   APTextFieldDelegateProxy             *delegateProxy                // Proxy. 
//The two properties below are not recommended any more, because the APNumericKeyboard is recommended for entering the ID card number. 
@property(strong, nonatomic)   UIButton             *buttonX                // The key to input the "x" of the ID card. 
@property(assign, nonatomic)   BOOL                 keyboardTypeIDCard   // Whether it is an ID card. 


typedef NS_ENUM(NSInteger,APTextFieldStyle){ 
       APTextFieldStylePlain,
      APTextFieldStyleBordered,
};
typedef NS_ENUM(NSInteger,APKeyboardType){
      APKeyboardTypeIDCard = 100,
}```

Example:

```C
_textField = [[APTextField alloc] initWithFrame:UIEdgeInsetsInsetRect(self.bounds, UIEdgeInsetsMake(0, 0, 1, 0))];
_textField.validator = [APTextValidator mobileNumberValidator];
```

## APTipView
Class name:  APTipView, inherited from UIView. 

Description: The tip view. 

Interface:
```C
/**
 *  Create a tip view with clickable button(s). 
 *  @param frame     The position and size in the parent view.
 *  @param tip   The tip text. 
 *  @param title      Button title.
 *  @return The object of the tip view with clickable button(s). 
 */
- (id)initWithFrame:(CGRect)frame tipMessage:(NSString *)tip title:(NSString *)title;

/**
 *  Create a tip view. 
 *  @param frame     The position and size in the parent view.
 *  @param tip   The tip text.
 *  @return The tip view object. 
 */
- (id)initWithFrame:(CGRect)frame tipMessage:(NSString *)tip;
```
Property:
```C
/** APTipViewDelegate. It must implement the delegate methods of clickable buttons. 
 * - (void)tipButtonClick:(UIButton *)sender;
 */
@property (nonatomic, weak) id <APTipViewDelegate> delegate;
```
Example:
```C
self.tipView = [[APTipView alloc] initWithFrame:self.view.frame tipMessage:[NSString stringWithFormat:@"No %@. Please try again later. ", self.title]];
[self.view addSubview:self.tipView];
```
Demo: 

![IMG_1079](https://t.alipayobjects.com/images/rmsweb/T12.RfXoNbXXXXXXXX.png)

## APToastView
Class name:  APToastView, inherited from UIView. 

Description: Toast message. 

Interface:
```C
/**
 * Show toast. 
 *
 * @param superview The view in which the toast will be shown. 
 * @param icon The icon type. The following types are supported: 
 *   APToastIconNone No icon. 
 *   APToastIconSuccess Success icon. 
 *   APToastIconFailure Failure icon. 
 * @param text Text. 
 * @param duration Duration. 
 *
 */
+ (void)presentToastWithin:(UIView *)superview withIcon:(APToastIcon)icon text:(NSString *)text duration:(NSTimeInterval)duration;

/**
 * @param superview The view in which the toast will be shown.
 * @param text Text.
 * @return The toast object shown. 
 */
+ (APToastView *)presentToastWithin:(UIView *)superview text:(NSString *)text;

/*
 * Modality view prompt. In this mode the screen will not respond to user operations. 
 */
+ (APToastView *)presentToastWithText:(NSString *)text;

/*
 * Modality toast
 * @param superview Parent view. 
 */
+ (APToastView *)presentModelToastWithin:(UIView *)superview text:(NSString *)text;

/*
 * Make the toast disappear. 
 */
- (void)dismissToast;

/**
 * Show toast.
 *
 * @param superview The view in which the toast will be shown.
 * @param icon The icon type. The following types are supported:
 *   APToastIconNone No icon.
 *   APToastIconSuccess Success icon.
 *   APToastIconFailure Failure icon.
 * @param text Text.
 * @param duration Duration.
 * @param completion Callback after the toast disappears automatically. 
 *
 */
+ (void)presentToastWithin:(UIView *)superview withIcon:(APToastIcon)icon text:(NSString *)text duration:(NSTimeInterval)duration completion:(void (^)())completion;

/**
 * Show modality toast. 
 *
 * @param superview The view in which the toast will be shown.
 * @param icon The icon type. The following types are supported:
 *   APToastIconNone No icon.
 *   APToastIconSuccess Success icon.
 *   APToastIconFailure Failure icon.
 * @param text Text.
 * @param duration Duration.
 * @param completion Callback after the toast disappears automatically.
 *
 */
+ (void)presentModalToastWithin:(UIView *)superview withIcon:(APToastIcon)icon text:(NSString *)text duration:(NSTimeInterval)duration completion:(void (^)())completion;
```
Example:
```C
[APToastView  presentToastWithin:weakSelf.view withIcon:ALPToastIconNone text:@"Quick pay cannot be activated for this card. Please change to another card." duration:2.0];
```
Demo:

![toast](https://t.alipayobjects.com/images/rmsweb/T1GopfXiBiXXXXXXXX.png)

## APRefreshTableHeaderView
Description: The pull-to-refresh widget encapsulated based on open-source EGORefreshTableHeaderView. 

## APTableViewCell
Interface:
```C
/**
 *  Constructor. The derived class calls the base class constructor. 
*/
- (id)initWithStyle:(UITableViewCellStyle)style reuseIdentifier:(NSString *)reuseIdentifier;
```
Property:
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

1.1 Class name: 
APTableViewBaseCell

1.2 Inheritance: 
1.3 Applicable scope:  It meets the needs in most cases of single-row cells. 

3. Operation and action: 
3.1 Interface: 

```C
+ (float)cellHeight;

/**
 * Reset all the child controls. This method should be called for reuse. 
 */
- (void)reset;

/**
 * Set the avatar icon. 
 */
- (void)setLogoImg:(UIImage *)img;

/**
 *  Attention: The SDWebImage.framework should be imported for this method to take effect.  Set the avatar to a specified URL, and set the default avatar. The defaultImg argument cannot be empty. 
 *
 *  @param imgUrl     Image URL. 
 *  @param defaultImg Default image UIImage instance. 
 */
- (void)setLogoImgWithUrl:(NSString *)imgUrl withDefault:(UIImage *)defaultImg;

/**
 * Set the title. 
 */
- (void)setTitle:(NSString* )title;

/**
 * Set whether to show the "Extend" icon. 
 */
- (void)setExtendLogo;

/**
 * Set the default background color. 
 */
- (void)setNormalBackground:(UIColor *)normalColor;

/**
 * Set the background color in selected status. 
 */
- (void)setSelectedBackground:(UIColor *)selectedColor;

/**
 * Set the prompt message. It provides additional information to the title and is shown on the right side. If the "Extend" icon exists, the prompt will be shown on the left of the "Extend" icon. 
 */
- (void)setInfo:(NSString *)info;

/**
 * Set the font color of the prompt message. 
 */
- (void)setInfoFontColor:(UIColor *)color;

/**
 * Design the prompt icon. The position is the same as above. The prompt message and prompt icon cannot coexist. You can only set to show one of them. 
 */
- (void)setInfoImg:(UIImage *)img;

/**
 * Set switches. 
 * @prama isOpen Default switch settings. 
 * @prama target Responding object of switch events. 
 * @prama selector Responding method of switch events. 
 */

- (void)setSwitchWithDefault:(BOOL) isOpne withTarget:(id) target withSelector:(SEL) selector;

/**
 * Set specific details. For the effect not existing in this class, Richcell can be used for the settings. 
 */
- (void)setInfoDetail:(NSString *)detail;

- (BOOL)isShowExtend;

- (void)addView:(UIView *)subView;

- (void)enableLineImageView:(BOOL)yesOrNo;

- (NSInteger )contentViewRightGap;
```

2.2 Callback event: 
2.3 Others:
Attention: To use (void)setLogoImgWithUrl:(NSString *)imgUrl withDefault:(UIImage *)defaultImg, the SDWebImage.framework needs to be imported for the interface to take effect.  Set the avatar to a specified URL, and set the default avatar. The defaultImg argument cannot be empty.

3. Example: 

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

    [cell setTitle:@"Alipay"];
    UIImage *img = [UIImage imageNamed:@"icon_58.png"];
    [cell setInfo:@"alipay"];
    [cell setLogoImg:img];

    return cell;
}
```

## APButtonCell
Class name:  APButtonCell, inherited from UITabbleViewCell. 

Description: Tables with buttons. 

Interface:
```C
/**
 * Method to add buttons in tables. 
 */
- (void)addButton:(APButton *)button;


/**
 * Add a type of button of the click event in the table. 
 */
- (APButton *)addButtonWithType:(APButtonType)buttonType title:(NSString *)title target:(id)target action:(SEL)action;
```

Example:
```C
APButtonCell *buttonCell = ??[APButtonCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:reuseIdentifier];
APButton *button = ?[APButton  alloc] initWithButtonType:APButtonTypeCustom];
[button setTitle:@"OK"];
?[buttonCell addButton:button];
[buttonCell addButtonWithType:APButtonTypeCustom title:@"OK" target:self  action:@selector(confirm:)];
```

## APInputBoxCell
Class name:  APInputBoxCell, inherited from UITabbleViewCell. 

Description: Tables with buttons.

Interface:

```C
/**
 * Widget height. 
 */
+ (float)cellHeight;


/**
 * Return the input box in the cell. 
 */
- (APInputBox *)textFieldInCell;
```

Example:
```C
APInputBoxCell *inputBoxCell = ??[APInputBox alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:reuseIdentifier];
APInputBox *box = [inputBoxCell textFieldInCell];
```

## APSwitchCell
1. Description of the widget: 
1.1 Class name:
APSwitchCell

1.2 Inheritance:
1.3 Applicable scope: It is used for settings. 
2. Interface property
2.1 Effect: 

![screenshot1392626534864](https://t.alipayobjects.com/images/rmsweb/T1fUNfXjBdXXXXXXXX.png)

3. Operation and action:
3.1 Interface:

```C
/**
 *  Return the cell height. 
 *
 *  @return The height value. 
 */
+ (float)cellHeight;

/**
 * Initialization
 *
 *  @param reuseIdentifier The reuse ID of the cell. 
 *
 *  @return APSwitchCell
 */
- (id)initWithReuseIdentifier:(NSString *)reuseIdentifier;

/**
 *  UISwitch widget
 */
@property (nonatomic, readonly) UISwitch *switchControl;
```

3.2 Callback event:
3.3 Others:
4. Example:

```C
APSwitchCell *cell = nil;
identifier = @"switch";
cell = [_tableView dequeueReusableCellWithIdentifier:identifier];
if (!cell) {
    cell = [[APSwitchCell alloc] initWithReuseIdentifier:identifier];
    cell.textLabel.text = @"Gesture password";
    cell.selectionStyle = UITableViewCellSelectionStyleNone;
    _tableView.separatorStyle = UITableViewCellSeparatorStyleSingleLine;
}
```

## APTableViewDoubleLineCell
Class name:  APTableViewDoubleLineCell, inherited from APTableViewCell. 

Description: The cell with double lines. 

Interface:

```C
/**
 *  APTableViewDoubleLineCell The initialization function. The set style is UITableViewCellStyleDefault. 
 *  We recommend this interface for initialization. 
 *  @param reuseIdentifier The reuse identifier. 
 *  @return The initialized instance. 
 */
- (id)initWithReuseIdentifier:(NSString *)reuseIdentifier;

/**
 *  Return the cell height. 
 *  @return The cell height. 
 */
+ (float)cellHeight;


/**
 *  Cell reset */
- (void)reset;
```
Example:

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
1.1 Class name: 
APTableViewTwoTextCell

1.2 Inheritance: 
Parent class:   APTableViewCell

1.3 Applicable scope
The cell with two text tags on the left and right respectively. 

The two tags will be aligned automatically according to the text length. 

2. Interface property
2.1 Effect:

![Screen Shot 2014-02-18 at 4.10.39 PM](https://t.alipayobjects.com/images/rmsweb/T10.hfXo8jXXXXXXXX.png)

4. Example:
```C
cell = [[APTableViewTwoTextCell alloc] 

    initWithStyle:UITableViewCellStyleDefault 

    reuseIdentifier:cellId 

    leftText:@"Text on the left" 

    rightText:@"Text on the right"]];
```

## APTextFieldCell
1.1 Class name:
APTextFieldCell

1.2 Applicable scope: It is used for input boxes in the cell. 

1.3 Effect: 
![screenshot1392627786282](https://t.alipayobjects.com/images/rmsweb/T1ooxfXoxgXXXXXXXX.png)

1.4 Interface: 

```C
@property(nonatomic, strong, readonly) APTextField *textField;
@property(nonatomic, assign) CGFloat textLabelWidth;
```
