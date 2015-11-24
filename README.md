# Project 101 Inc Objective-C Style Guide

This was forked from The New York Times Objective-C Style Guide to meet our team coding conventions and style.

## Introduction

Here are some of the documents from Apple that informed the style guide. If something isn't mentioned here, it's probably covered in great detail in one of these:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Table of Contents

* [Dot-Notation Syntax](#dot-notation-syntax)
* [Spacing](#spacing)
* [Pragma Mark](#pragma-mark)
* [Conditionals](#conditionals)
  * [Ternary Operator](#ternary-operator)
* [Error handling](#error-handling)
* [Methods](#methods)
* [Variables](#variables)
* [Naming](#naming)
  * [Underscores](#underscores)
* [Enums](#enums)
* [Comments](#comments)
* [Init & Dealloc](#init-and-dealloc)
* [Literals](#literals)
* [CGRect Functions](#cgrect-functions)
* [Constants](#constants)
* [Enumerated Types](#enumerated-types)
* [Models](#models)
* [Private Properties](#private-properties)
* [Image Naming](#image-naming)
* [Booleans](#booleans)
* [Run in background](#run-in-background)
* [Singletons](#singletons)
* [Xcode Project](#xcode-project)

## Dot-Notation Syntax

Dot-notation should be used wherever possible for accessing and mutating properties, since it's more readable.

**For example:**
```objc
view.backgroundColor = UIColor.orangeColor;
label.text = @"hi there";
```

Using bracket notation carries one advantage with Xcode auto-complete by providing guidance on the parameter type.

## Spacing

* Indent using 4 spaces. Be sure to set this preference in Xcode.
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.

**For example:**
```objc
if (user.isHappy) {
//Do something
}
else {
//Do something else
}
```
* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but often there should probably be new methods.
* `@synthesize` and `@dynamic` should each be declared on new lines in the implementation.

## Pragma Mark

`#pragma mark` should always be used to group methods into functional or logical sections. Below are the usual `#pragma mark`s.

`#pragma mark - View Lifecycle`
viewDidLoad
viewWillAppear
didReceiveMemoryWarning

`#pragma mark - IB Methods` OR `#pragma mark - IB Actions`
Any method from Interface Builder.

`#pragma mark - Private Methods`
Methods accessible only within the implementation file.

`#pragma mark - Public Methods`
Methods exposed in the interface files.

`#pragma mark - Service Methods`
Methods exposed in the interface files that are meant to provide some sort of a service.

`#pragma mark - Utilities`
Methods that provide utilities such as serialization, calculation, formatting, etc.

`#pragma mark - Handle Notifications`
Methods that handle notifications from NSNotificationCenter.

`#pragma mark - Handle Gestures`
Methods that handle gestures such as tap and swipe.

When using a delegate or datasource, just copy and paste the actual name into the pragma mark. This allows you to `Option + Click` it for the documentation within the pragma mark.

`#pragma mark - UITableViewDelegate`

`#pragma mark - UITableViewDataSource`

`#pragma mark - UIAlertViewDelegate`

`#pragma mark - UIScrollViewDelegate`

`#pragma mark - SomeCustomDelegate`

There should be exactly one empty line before and after the `#pragma mark` directive

## Conditionals

Conditional bodies should always use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent [errors](https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256). These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) may happen where the line "inside" the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

**For example:**
```objc
if (!error) {
    return success;
}
```

**Not:**
```objc
if (!error)
    return success;
```

or

```objc
if (!error) return success;
```

### Ternary Operator

The Ternary operator, ? , should only be used when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an if statement, or refactored into instance variables.

**For example:**
```objc
result = a > b ? x : y;
```

**Not:**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## Error handling

When methods return an error parameter by reference, switch on the returned value, not the error variable.

**For example:**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

**Not:**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Handle Error
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).

## Methods

In method signatures, there should be a space after the scope (-/+ symbol). There should be a space between the method segments.

**For Example**:
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```

For method declaration, do not declare the method on the @interface area of the implementation file because it is no longer necessary. For example:

**For Example**:
```objc
@interface SomeClass ()

@end

@implementation

-(void)doSomething {
	// code here
}

@end
```

**Not:**
```objc
@interface SomeClass ()

-(void)doSomething; // unnecessary

@end

@implementation

-(void)doSomething {
	// code here
}

@end
```

## Variables

Variables should be named as descriptively as possible. Single letter variable names should be avoided except in `for()` loops.

Asterisks indicating pointers belong with the variable, e.g., `NSString *text` not `NSString* text` or `NSString * text`, except in the case of constants.

Property definitions should be used in place of naked instance variables whenever possible. Direct instance variable access should be avoided.

**For example:**

```objc
@interface NYTSection: NSObject

@property (nonatomic) NSString *headline;

@end
```

**Not:**

```objc
@interface NYTSection : NSObject {
    NSString *headline;
}
```

## Naming

Apple naming conventions should be adhered to wherever possible, especially those related to [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Long, descriptive method and variable names are good. In addition, to work better with Xcode autocomplete, the name structure is a combination and order of the object name (eg. button, label) followed by the object description (eg. Done, Cancel, Status).

**For example:**

```objc
UIButton *buttonSettings;
```

**Not**

```objc
UIButton *setBut;
```

**And Not**

```objc
UIButton *settingsButton;
```

A three letter prefix (e.g. `NYT`) should always be used for class names and constants, however may be omitted for Core Data entity names. Constants should be camel-case with all words capitalized and prefixed by the related class name for clarity.

**For example:**

```objc
static const NSTimeInterval NYTArticleViewControllerNavigationFadeAnimationDuration = 0.3;
```

**Not:**

```objc
static const NSTimeInterval fadetime = 1.7;
```

Properties should be camel-case with the leading word being lowercase. **If Xcode can automatically synthesize the variable, then let it.** Otherwise, in order to be consistent, the backing instance variables for these properties should be camel-case with the leading word being lowercase and a leading underscore. This is the same format as Xcode's default synthesis.

**For example:**

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**Not:**

```objc
id varnm;
```

### Underscores

When using properties, instance variables should always be accessed and mutated using `self.`. This means that all properties will be visually distinct, as they will all be prefaced with `self.`. Local variables should not contain underscores.

## Enums

For enums, use NS_ENUM macro.

**For example:**

```objc
typedef NS_ENUM(NSInteger, SomeEnum) {
	SomeEnumDefault = 0,
	SomeEnumTypeA = 1,
	SomeEnumTypeB = 2,
};
```

**Not:**

```objc
typedef enum {
	SomeEnumDefault,
	SomeEnumTypeA,
	SomeEnumTypeB
}SomeEnum;
```

## Comments

When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted. Long comments should generally be avoided, as code should be as self-documenting as possible with only the need for brief descriptions. 

We will be using appledoc for generating documentation, so the following syntax is required for comments included in documentation generation.

**Syntax**

```objc
/** 
 Add new message between source to destination timeline as empty name string
 @param sourceId Source timeline entity ID
 @param destId Destination timeline entity ID
 @see someMethodWithString:
 @warning *Warning:* A blue background.
 @bug *Bug:* A yellow background.
 @returns A newly created message instance
 */
```
The foward slash and double asterisks will be picked up by appledoc for documentation generation.

The following comment syntax will not be picked up by appledoc and can still be used for comments that should not be included in documentation generation.
```objc
/* comment */

// comment
```

In header files, the interface, every property, and every method **must** have appledoc compliant comments.

The details of appledoc installation and guidelines can be found on [Confluence](http://confluence/display/PdM/Code+documentation+and+commenting).

## init and dealloc, super

`dealloc` methods should be placed at the top of the implementation, directly after the `@synthesize` and `@dynamic` statements. `init` should be placed directly below the `dealloc` methods of any class.

`init` methods should be structured like this:

```objc
- (instancetype)init {
    self = [super init]; // or call the designated initalizer
    if (self) {
        // Custom initialization
    }

    return self;
}
```

In general, `super` should be the first thing you call in a method. 

**For example:**
```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    
    // then the rest of the code

}
```

**Not:**
```objc
- (void)viewDidLoad {
     
     // some code here
     
    [super viewDidLoad];
}
```


However, teardown `super` should be placed at the end of the method. For example:

**For example:**
```objc
- (void)viewWillDisappear {
   // do your stuff here
   // ...
   
   [super viewWillDisappear];
}
```

For object initialization, avoid using `new` and follow the conventional `alloc` `init`. 

**For example:**

```objc
NSMutableArray *array = [[NSMutableArray alloc] init];
```

**Not:**

```objc
NSMutableArray *array = [NSMutableArray new];
```


## Literals

`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

**For example:**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
productManagers[@"iPhone"];
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**Not:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
[productManagers objectForKey:@"iPhone"];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## CGRect Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**For example:**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**Not:**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

## Constants

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro.

**For example:**

```objc
static NSString * const NYTAboutViewControllerCompanyName = @"The New York Times Company";

static const CGFloat NYTImageThumbnailHeight = 50.0;
```

**Not:**

```objc
#define CompanyName @"The New York Times Company"

#define thumbnailHeight 2
```
However, for singleton + notification pattern, use #define over constants.

## Enumerated Types

When using `enum`s, it is recommended to use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types — `NS_ENUM()`

**Example:**

```objc
typedef NS_ENUM(NSInteger, NYTAdRequestState) {
    NYTAdRequestStateInactive,
    NYTAdRequestStateLoading
};
```

## Private Properties

Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. Named categories (such as `NYTPrivate` or `private`) should never be used unless extending another class.

**For example:**

```objc
@interface NYTAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

## h vs m files

In general, all properties, methods, and imports should be in the m file. Decide carefully which property, method, or import should be in the h file.

Any property or method in the h file should be accompanied by proper comments.

## Image Naming

Image names should be named consistently to preserve organization and developer sanity. They should be named as one camel case string with a description of their purpose, followed by the un-prefixed name of the class or property they are customizing (if there is one), followed by a further description of color and/or placement, and finally their state.

**For example:**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` and `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` and `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

Images that are used for a similar purpose should be grouped in respective groups in an Images folder.

## Booleans

Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

This allows for more consistency across files and greater visual clarity.

**For example:**

```objc
if (!someObject) {
}
```

**Not:**

```objc
if (someObject == nil) {
}
```

-----

**For a `BOOL`, here are two examples:**

```objc
if (isAwesome)
if (![someObject boolValue])
```

**Not:**

```objc
if ([someObject boolValue] == NO)
if (isAwesome == YES) // Never do this.
```

-----

By default, the default attribute for properties are `atomic`, `readwrite`. For object, in most cases, it will default to `strong`. For primitives, it is default to `assign`.

**For Example:**
```objc
@property CGFloat hello;
@property NSString *bye;
```
**Same As:**
```objc
@property (atomic, readwrite, assign) CGFloat hello;
@property (atomic, readwrite, strong) NSString *bye;
```

For BOOL property declaration, use this style unless there is special circumstance that requires specific attributes.

**For Example:**

```objc
@property (nonatomic) BOOL editable;
```

**Not:**

```objc
@property (nonatomic, assign) BOOL editable;
```

For `strong` property declaration, use this style.

**For Example:**

```objc
@property (nonatomic) NSString *helloWorld;
```

**Not:**

```objc
@property (nonatomic, strong) NSString *helloWorld;
```

For `weak` property declaration, use this style.

**For Example:**

```objc
@property (nonatomic, weak) UILabel *labelStatus
```

Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE).

## Run in background

When you need to run something in the background, use GCD.

**For Example:**

```objc
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        
        // your background stuff here
        
        dispatch_async(dispatch_get_main_queue(), ^{
            
			// perform UI updates on main thread
            
        });
    });
```

In addition, do not create a serial queue when you do not need it, just use the example above.


## Singletons

Singleton objects should use a thread-safe pattern for creating their shared instance.
```objc
+ (instancetype)sharedInstance {
   static id sharedInstance = nil;

   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });

   return sharedInstance;
}
```
This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

## Logging

NSLog is the default console log output function. However, to have this running in production is both unnecessary and can create performance issues.

We have created a logging macro that only generates output when the DEBUG macro exists and also includes the function name and line number. 

Avoid using NSLog and use DLog only.

## Xcode project

The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created should be reflected by folders in the filesystem. Code should be grouped not only by type, but also by feature for greater clarity.

When possible, always turn on "Treat Warnings as Errors" in the target's Build Settings and enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).
