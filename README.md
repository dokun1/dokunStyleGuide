# My Objective-C Style Guide

Hi! When I write my code, I try my best to standardize everything, and this is a style guide for how I try to write my code. I'll add to this as time goes on, but I wanted to give you (the reader) a sample of how I do things!

### Disclosure

This document is heavily modeled in both structure and content after the [New York Times Obj-C Style Guide](https://github.com/NYTimes/objective-c-style-guide/blob/master/README.md).

## Table of Contents

* [Spacing](#spacing)
* [Conditional Code](#conditional-code)
* [Documentation](#documentation)
* [Constants](#constants)
* [Literal Notation](#literal-notation)
* [Core Graphics](#core-graphics)
* [Other](#other)

## Spacing

* I keep all text at a minimum of 4 spaces away from the left hand side of the screen. This can be pre-set in xcode, and is standard on every machine.

Observe the following method signature:

```obj-c
- (void)methodThatDoesSomething:(NSString *)stringParameterOne andAnotherThing:(BOOL)secondParameter {
     
}
```

* The returned value in parentheses should always have one space between it and the dash that signals a method.
* When opening a method, I put the opening bracket one space away from the end of the method signature.
* I strictly follow camel-case for spelling of methods and capitalization.
* I avoid using numbers in signatures, but if I must, I write them out as words.
* When typing parameters, if I need to include a pointer, I put a space between the object type and the pointer.
* I avoid using generic names for parameters, such as "string" or "array". I type in favor of being specific as opposed to being short.
* I only hit return after each parameter if there are more than four parameters, like so:


```obj-c
- (void)methodWithLotsOfParameters:(NSString *)paramOne
                     nextParameter:(NSString *)paramTwo
                     nextParameter:(NSString *)paramThree
                     nextParameter:(NSString *)paramFour
                     nextParameter:(NSString *)paramFive;
```

* Observe the following arrays:

```obj-c
NSArray *arrayOne = @[@"Hello", @"World", @"Again"];
NSArray *arrayTwo = @[@"This is a really long string value",
                      @"Oh hey, cool, another really long string",
                      @"What? Another one!"];
```

* As often as I can, I write arrays and dictionaries in line.
* If the elements of either set are individually longer than one word or 'thing', I hit return after every element so that the @ symbols line up.
* Note the use of [literal notation](#literal-notation), which is covered later.

##Conditional Code

* While [ternary operators](https://agilewarrior.wordpress.com/2012/09/21/objective-c-ternary-operator/) generally make things simpler and reduce the amount of code written, I avoid using them in code in lieu of properly spaced if-else statements, like so:

```obj-c
if (checkBool) {
    //do stuff because checkBool is true
} else {
    //do other stuff because checkBool is false
}
```

* Note the consistency in spacing, particularly with the else statement, as there is a space on both sides of the word
* The clear separation of code blocks allows for easier understanding when someone is reading for the first time.

Observe the following method:

```obj-c
- (void)doSomethingWithSuccess:(void (^)(NSArray *results))successBlock failure:(void (^)(NSError *error))failureBlock {
    if (callSuccessful) {
        successBlock(@[@"testing"]);
    } else {
        NSError *error = [NSError errorWithDomain:@"co.uk.IDscan" code:404 userInfo:@{}];
        failureBlock(error);
    } 
}
```

* This notation usually best applies when writing API calls.
* It is important to have separate success and failure blocks for ease of understanding and very distinct error handling, like so:

```obj-c
[self doSomethingWithSuccess:^(NSArray *results) {
    //where to do things if the call was successful
} failure:^(NSError *error) {
    //where to do things if the call was not successful
}];
```

##Documentation

* I automatically generate iOS documentation using [headerdoc2html](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/HeaderDoc/usage/usage.html), so I like to document my code in the following ways:

###Properties:

```obj-c
/*!
 * @brief A property of the device that I am controlling
 *
 * @discussion This is a place to put any extra notes that might be pertinent to the property I am documenting
 */
@property (strong, nonatomic) NSString *property;
```

###NS_ENUM

```obj-c
/*!
 *  @brief  An enumerated parameter to demonstrate the quality of the feature
 *
 */
typedef NS_ENUM(NSInteger, DMOQuality){
    /*!
     *  @brief  Could not score the feature due to an error
     */
    DMOQualityFeatureUnknown = -1,
    /*!
     *  @brief  A high score for recognizing the feature
     */
    DMOQualityFeatureBest    = 0,
    /*!
     *  @brief  Quality of detected feature is not that good
     */
    DMOQualityFeatureReduced = 1
};
```

###Methods

```obj-c
/*!
 *  @brief  Delegate method when device fails
 *
 *  @param device DMODevice that I am controlling in this code
 *  @param error  NSError with info about error
 *
 */
- (void)device:(DMODevice *)device didFailWithError:(NSError *)error;
```

###Interfaces

```obj-c
/*!
 * @brief  All documentation goes here. Some more comments about the interface
 */
@interface DMOController : UIViewController
```

##Constants

* I think it is very important to ensure that there are very rarely, if ever, literal strings embedded in my implementation code.
* Defining a constant for a string or numeric value should look like so, between my import statements and interface/implementation:

```obj-c
static NSString *const kConstantValueOne = @"this is the string for this constant";
static CGFloat const kConstantFloatValue = 12.5f;
```

* This will allow constants to be recognizable privately in the class. To promote these constants to public, I type this in my header:

```obj-c
extern NSString *const kConstantValueOne;
extern CGFloat const kConstantFloatValue;
```

* Note that all constants should start with a k, as is Apple's standard for constant naming. After that, if the constant is to be made public, I follow project namespace conventions for the beginning of the constant name if needed.

##Literal Notation

* Objective-C 2.0 invoked literal notation, ridding me of this...:

```obj-c
[NSArray arrayWithObjects:@"Hello", @"World", nil];
[NSDictionary dictionaryWithObjectsAndKeys:@"Hello", @"First", @"World", @"Second", nil];
[NSNumber numberWithFloat:12.5f];
[NSNumber numberWithBool:YES];
```

* ...and giving me the ability to do this:

```obj-c
@[@"Hello", @"World"];
@{@"Hello":@"First", @"World":@"Second"};
@(12.5f);
@YES;
```

* I use the second convention whenever possible!

##Core Graphics

* When using CGRect, there is a specific set of functions for determining data points about the rectangle. Instead of doing this:

```obj-c
CGRect frame;

CGFloat originX = frame.origin.x;
CGFloat originY = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

* I do this instead:

```obj-c
CGRect frame;

CGFloat originX = CGRectGetMinX(frame);
CGFloat originY = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

You can view the documentation for all of these methods [here](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Reference/CGGeometry/index.html).

##Other

* Please email me at david@okun.io if you have other points you would like to add to this style guide.
