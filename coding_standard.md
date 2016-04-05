# iOS Coding Standards

These standards outline the coding conventions of our iOS team and are based on the almost perfect [NYTimes Objective-C Style Guide](https://github.com/NYTimes/objective-c-style-guide).

## Table of Contents

* [Imports](#imports)
* [Spacing](#spacing)
* [Conditionals](#conditionals)
  * [Ternary Operator](#ternary-operator)
* [Error handling](#error-handling)
* [Methods](#methods)
* [Variables](#variables)
* [Types](#types)
* [Properties](#properties)
* [Naming](#naming)
  * [Underscores](#underscores)
* [Comments](#comments)
* [Init & Dealloc](#init-and-dealloc)
* [Literals](#literals)
* [CGRect Functions](#cgrect-functions)
* [Constants](#constants)
* [Enumerated Types](#enumerated-types)
* [Private Properties](#private-properties)
* [Booleans](#booleans)
* [Singletons](#singletons)
* [Xcode Project](#xcode-project)

## Imports

Imports should be grouped into local and framework imports, and local imports
should be listed first. Within those groups, imports should be listed in alphabetical order.

**For example:**


    #import "AFNetworking.h"
    #import "CSComScore.h"
    #import "XXXAnalyticsManager.h"
    #import "XXXAppDelegate.h"
    
    #import <AdSupport/AdSupport.h>
    #import <AssetsLibrary/AssetsLibrary.h>
    #import <Crashlytics/Crashlytics.h>

## Spacing

* Indent using 4 spaces. Never indent with tabs. Be sure to set this preference in Xcode.
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open and close on a new line.

**For example:**

    if (user.isHappy) 
    {
        //Do something
    }
    else 
    {
        //Do something else
    }

* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality logically.
* `@synthesize` (when necessary) and `@dynamic` should each be declared on new lines in the implementation.
* You are encouraged to categorize methods into groups with `#pragma mark` directives.

Use spaces to line up groups of properties and instance variables into ordered columns, unless you are using block comments to document them.

**For example:**

    @property (nonatomic, weak)     id<XXXGalleryImageViewDelegate>  delegate;
    @property (nonatomic, readonly) XXXGalleryItem                  *gallery;
    @property (nonatomic, readonly) NSString                        *feedKey;

**And:**

    @implementation XXXGalleryImageView
    {
        UIView      *_containerView;
        UIImageView *_currentImageView;
        UIView      *_mask;
    }

## Conditionals

Conditional bodies should always use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent errors. These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) may happen where the line "inside" the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

**For example:**

    if (!error) 
    {
        return success;
    }

**Not:**

    if (!error)
        return success;

or

    if (!error) return success;

### Ternary Operator

The Ternary operator, ? , should only be used when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an if statement, or refactored into instance variables.

**For example:**

    result = a > b ? x : y;

**Not:**

    result = a > b ? x = c > d ? c : d : y;

## Error handling

When methods return an error parameter by reference, switch on the returned value, not the error variable.

**For example:**

    NSError *error;
    if (![self trySomethingWithError:&error]) 
    {
        // Handle Error
    }

**Not:**

    NSError *error;
    [self trySomethingWithError:&error];
    if (error) 
    {
        // Handle Error
    }

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).

## Methods

In method signatures, there should be a space after the scope (-/+ symbol). There should be a space between the method segments.

**For Example**:

    - (void)setExampleText:(NSString *)text image:(UIImage *)image;

## Variables

Variables should be named as descriptively as possible. Single letter variable names should be avoided except in `for()` loops.

Asterisks indicating pointers belong with the variable, e.g., `NSString *text` not `NSString* text` or `NSString * text`, except in the case of constants.

Private instance variables should be declared in the implementation, not in the interface.

**For example:**

    @implementation XXXItem
    {
        NSString *_value;
    }
    
    @end

**Not:**

    @interface XXXItem : NSObject 
    {
        NSString *_value;
    }

## Types

When passing arguments to a method or assigning the return value to a local variable, when possible ensure that the argument or local variable is the correct type. If a method returns an `NSInteger`, assign its return value to a local variable of type `NSInteger`, not `int` or `long`. If a method parameter expects a `CGFloat`, pass an argument of type `CGFLoat`, not `float` or `double`. This will ensure that code is portable across architectures, where the definitions of `NSInteger` and `CGFloat` may differ.

Similarly, when writing literal primitives, when possible use the correct format:

    CGFloat    f = 5.0f;
    NSInteger  i = 6;
    NSUInteger u = 5u;
    double     d = 2048.0;    

## Properties

When a property has been declared, use it and do not access the backing instance variable directly except within initializer methods (`init`, `initWithCoder:`, etc…), `dealloc` methods and within custom setters and getters. For more information on using Accessor Methods in Initializer Methods and dealloc, see [here](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6).

## Naming

Apple naming conventions should be adhered to wherever possible and can be found here: [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html). Long, descriptive method and variable names are good.

**For example:**

    UIButton *settingsButton;

**Not**

    UIButton *setBut;

A three letter prefix (e.g. `XXX`) should always be used for class names and external constants, however may be omitted for Core Data entity names. Constants should begin with a 'k', be camel-case with all words capitalized and prefixed by the related class name for clarity.

**For example:**

    extern const CGFloat kXXXUploadProgressCellHeight;

**Not:**

    extern const CGFloat uploadProgressCellHeight;

Static constants (used within a single object file and never externed) do not need to be prefixed with a related class name, but should still be prefixed with a 'k'.

**For example:**

    static const CGFloat kNormalCellHeight = 75.0f;

Properties should be camel-case with the leading word being lowercase. **If Xcode can automatically synthesize the variable, then let it.** Otherwise, in order to be consistent, the backing instance variables for these properties should be camel-case with the leading word being lowercase and a leading underscore. This is the same format as Xcode's default synthesis.

**For example:**

    @synthesize descriptiveVariableName = _descriptiveVariableName;

**Not:**

    id varnm;

### Underscores

Instance variables should always start with an underscore. Local variables should not contain underscores.

## Comments

In order to automatically generate documentation in Xcode 5, every class, public property, and public method should have at least one or two lines describing its purpose, formatted in [Doxygen format](http://www.stack.nl/~dimitri/doxygen/manual/docblocks.html#cppblock).

**For example:**

    /**
     A UIView subclass that accepts a layer mask
     and updates that mask's bounds as its own
     bounds change.
     */
    @interface XXXMaskedView : UIView

    @property (nonatomic, strong) CALayer *mask; ///< The layer used to mask the receiver's layer.

    /**
     The amount to inset (or outset) the 
     mask within the receiver's bounds.
     */
    @property (nonatomic) UIEdgeInsets maskInsets;

    @end

When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted.

Block comments should generally be avoided, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. This does not apply to those comments used to generate documentation.

## init and dealloc [init-and-dealloc]

`init` methods should be placed at the top of the implementation, directly after the `@synthesize` and `@dynamic` statements. `dealloc` should be placed directly below the `init` methods of any class.

`init` methods should be structured like this:

    - (instancetype)init 
    {
        self = [super init]; // or call the designated initalizer
        if (self) 
        {
            // Custom initialization
        }

        return self;
    }

## Literals

`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

**For example:**

    NSArray *names = @[@"Chris S", @"Matt", @"Les", @"Jeff", @"Chris H", @"Jerry", @"Luke"];
    NSDictionary *productManagers = @{@"iPhone" : @"Matt", @"Web" : @"Bettina", @"Mobile Web" : @"David"};
    NSNumber *shouldUseLiterals = @YES;
    NSNumber *buildingZIPCode = @90036;

**Not:**

    NSArray *names = [NSArray arrayWithObjects:@"Chris S", @"Matt", @"Les", @"Jeff", @"Chris H", @"Jerry", @"Luke", nil];
    NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Matt", @"iPhone", @"Bettina", @"Web", @"David", @"Mobile Web", nil];
    NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
    NSNumber *buildingZIPCode = [NSNumber numberWithInteger:90036];

## CGRect Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**For example:**

    CGRect frame = self.view.frame;

    CGFloat x = CGRectGetMinX(frame);
    CGFloat y = CGRectGetMinY(frame);
    CGFloat width = CGRectGetWidth(frame);
    CGFloat height = CGRectGetHeight(frame);

**Not:**

    CGRect frame = self.view.frame;

    CGFloat x = frame.origin.x;
    CGFloat y = frame.origin.y;
    CGFloat width = frame.size.width;
    CGFloat height = frame.size.height;

## Constants

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro.

**For example:**

    static NSString * const kXXXFacebookPageURL = @"http://www.facebook.com/profile.php?id=123456";
    
    static const CGFloat kNormalCellHeight = 75.0f;

**Not:**

    #define kXXXFacebookPageURL @"http://www.facebook.com/profile.php?id=123456"
    
    #define kNormalCellHeight 75.0f

## Enumerated Types

When using `enum`s, it is recommended to use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types — `NS_ENUM()`. The constants should all share a consistent prefix, which should match the second argument to the `NS_ENUM` macro.

**Example:**

    typedef NS_ENUM(NSInteger, PinchGestureIntent)
    {
        PinchGestureIntentUnknown,
        PinchGestureIntentFullscreen,
        PinchGestureIntentZoom
    };

## Private Properties

Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. Named categories (such as `XXXPrivate` or `private`) should never be used unless extending another class.

**For example:**

    @interface XXXMyViewController ()
    
    @property (nonatomic, strong) GADBannerView *googleAdView;
    @property (nonatomic, strong) ADBannerView  *iAdView;
    @property (nonatomic, strong) UIWebView     *adXWebView;
    
    @end

## Booleans

Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

This allows for more consistency across files and greater visual clarity.

**For example:**

    if (!someObject) 
    {
    }

**Not:**

    if (someObject == nil) 
    {
    }

-----

**For a `BOOL`, here are two examples:**

    if (isAwesome)
    if (![someObject boolValue])

**Not:**

    if ([someObject boolValue] == NO)
    if (isAwesome == YES) // Never do this.

-----

If the name of a `BOOL` property is expressed as an adjective, the property can omit the “is” prefix but specifies the conventional name for the get accessor, for example:

    @property (assign, getter=isEditable) BOOL editable;

Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE).

## Singletons

Singleton objects should use `dispatch_once` for creating their shared instance in a thread-safe manner. This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html). The name of the method for creating and accessing the shared instance should start with the word `shared` and end with the class name or a logical variation thereof.

**Example:**

    @implementation XXXFacebookManager

    + (XXXFacebookManager *)sharedFacebookManager
    {
        static XXXFacebookManager *sharedFacebookManager;
        static dispatch_once_t onceToken;
        dispatch_once(&onceToken, ^(void)
        {
            sharedFacebookManager = [[XXXFacebookManager alloc] init];
        });
    
        return sharedFacebookManager;
    }

## Xcode project

Enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible, and make sure that your code does not generate any. If you need to ignore a specific warning, use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas). Only disable a warning if you are sure it can be ignored without causing a bug, and then disable it for as few lines as possible. Never disable warnings for an entire file, class, or method.

If you are importing code from a third-party library that generates warnings, you may disable warnings for those files.

