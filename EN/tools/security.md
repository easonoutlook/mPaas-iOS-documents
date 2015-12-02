@cnName iOS APSecurityUtility
@priority 5

# iOS APSecurityUtility

[TOC]

## 1 Introduction

APSecurityUtility offers a series of useful security testing interfaces mainly including the runtime Hook checks, external call checks and debugging status checks. 

The application scenario should be explicated before you call the Hook testing interface.  You should select the correct testing interfaces according to the programming language, whether the framework API functions of the iOS system will be tested, or the self-defined functions. 

## 2 Interface introduction

### 2.1 APDetectClassSelectorSwizzling
```
/**
 *  This method checks whether the (+)selector is hooked by an injected library before calling the selector. 
 *  It only applies to the selector implemented within this application (including the static library), and does not apply to the framework API of the iOS system and API defined in other dynamic libraries. 
 * 
 *  (BOOL)APDetectClassSelectorSwizzling(NSString *className, SEL sel)
 *
 *  ------------------------------------------------------
 *  @param className selector Name of the class the selector belongs to. 
 *  @param sel       Selector
 *
 *  @return The returned value is of the BOOL type. When YES is returned, it indicates that this selector has been hooked. 
 *  ------------------------------------------------------
 *
 *  For example: 
 *  BOOL isHooked = APDetectClassSelectorSwizzling(@"SomeClass", @selector(test));
 *
 */
```

### 2.2 APDetectInstanceSelectorSwizzling
```
/**
 *  This method checks whether the (-)selector is hooked by an injected library before calling the selector. 
 *  It only applies to the selector implemented within this application (including the static library), and does not apply to the framework API of the iOS system and API defined in other dynamic libraries.
 *
 *  (BOOL)APDetectInstanceSelectorSwizzling(NSString *className, SEL sel)
 *
 *  ------------------------------------------------------
 *  @param className selector Name of the class the selector belongs to.
 *  @param sel       Selector
 *
 *  @return The returned value is of the BOOL type. When YES is returned, it indicates that this selector has been hooked.
 *  ------------------------------------------------------
 *
 *  For example:
 *  BOOL isHooked = APDetectInstanceSelectorSwizzling(@"SomeClass", @selector(test));
 *
 */
```

### 2.3 APDetectCFuncCalledByOthers & APDetectNonFrameworkSelectorCalledByOthers
```
/**
 *  This method is added in the internal implementation of C functions to check whether the function has been called by external injectors, such as GDB and injected dynamic libraries. 
 *
 *  For example: 
 *  bool isRisky = APDetectCFuncCalledByOthers;
 *
 */
```

### 2.4 APDetectUIKitInstanceSelectorSwizzling
```
/**
 *  This method checks whether the (-)selector in the UIKit framework is hooked by injected libraries before calling the selector, i.e., whether the system API has been hooked.
 *  It only applied to the API defined in the UIKit Framework of the iOS system. 
 *
 *  (BOOL)APDetectUIKitInstanceSelectorSwizzling(NSString *className, SEL sel)
 *
 *  ------------------------------------------------------
 *  @param className selector Name of the class the selector belongs to.
 *  @param sel       Selector
 *
 *  @return The returned value is of the BOOL type. When YES is returned, it indicates that this selector has been hooked.
 *  ------------------------------------------------------
 *
 *  For example:
 *  BOOL isHooked = APDetectUIKitInstanceSelectorSwizzling(@"UITextfield", @selector(text));
 *
 */
```

### 2.5 APDetectLocalAuthInstanceSelectorSwizzling
```
/**
 *  This method checks whether the (-)selector in the LocalAuthentication.framework is hooked by injected libraries before calling the selector, i.e., whether the system API has been hooked. 
 *  It only applied to the API defined in the LocalAuthentication.framework of the iOS system. 
 *  It does not apply to the simulator. 
 *
 *  Attention: The caller needs to ensure the linking with LocalAuthentication.framework (The optional configuration is available).  
 *       When systems lower than iOS8 cannot link the LocalAuthentication.framework, this method will return "NO" by default. 
 *
 *  (BOOL)APDetectLocalAuthInstanceSelectorSwizzling(NSString *className, SEL sel)
 *
 *  ------------------------------------------------------
 *  @param className selector Name of the class the selector belongs to.
 *  @param sel       Selector
 *
 *  @return The returned value is of the BOOL type. When YES is returned, it indicates that this selector has been hooked.
 *  ------------------------------------------------------
 *
 *  For example:
 *  BOOL isHooked = APDetectLocalAuthInstanceSelectorSwizzling(@"LAContext", @selector(canEvaluatePolicy:error:));
 *
 */
```

### 2.6 APDetectJailbroken
```
/**
 *  Jailbreak check. 
 *  
 *  (BOOL)APDetectJailbroken;
 */
```

### 2.7 APDetectDebugger
```
/**
 *  This method checks whether the current application is in the debugging status. 
 *  (BOOL)APDetectDebugger;
 *
 *  @return The returned value is of the BOOL type. When YES is returned, it indicates that the application is in the debugging status. 
 */
```

### 2.8 APGetTextSectionDigest
```
/**
 *  This method acquires the abstract of binary code snippets of executable programs. 
 *
 *  (NSString *)APGetTextSectionDigest;
 */
```

### 2.9 APDetectLinkerFlag
```
/**
 * This method checks the "restrict" flag in the linker flag of binary executable programs.  
 *
 *  (BOOL)APDetectLinkerFlag;
 *  @return The returned value is of the BOOL type. When YES is returned, it indicates that the "restrict" flag exists.
 */
```

### 2.10 APCheckNotificationSecurity
```
/**
 *  This method checks whether the notification sender is from inside the application after a notification is received.  When a third-party plug-in sends a notification named "name", it will return NO, indicating the notification may harbor risks.  
 *  Important: The notification name string must be a static string. If the sender concatenates the notification name with format, this method will return NO as the notification is not up to the expectation. 
 *
 *  Sample: 
 *
 *  NSString *name = [notification name];
 *  if (!APCheckNotificationSecurity(name)) {
 *      //Do not respond to notifications sent from outside the wallet. 
 *      return;
 *  }
 *
 *  @return The returned value is of the BOOL type. When YES is returned, it indicates that the notification is safe. 
 */
```
