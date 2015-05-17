Code conventions
================
These are my own conventions I make up as I go along learning about iOS development. Don't agree? Got suggestions? Write your own conventions file. Or start an issue.  
/teroqim

Objective-C
-----------

###General .m file structure
```objective-c
/*
Optional comment about what is being implemented.
*/

/*
Import all headers you need. Order them like this:
- Local header files. Always put the header for the class you're implementing at the top.
- C lib includes.
- Global header files.
Put one empty line between each of these categories.
*/
#import "MyClass.h"
#import "MyOtherClass.h"

#include <stdlib.h>

#import <AVFoundation/AVPlayer.h>

/*
Add private properties in an "empty" category for the class you're implementing. Try to group properties by function.
*/
@interface MyClass ()

@property AVPlayer *player;

@end

/*
Implementation.

Group methods using pragma marks with names that make sense. 
Put groups in the following order
- Superclass method overrides.
- Public interface methods.
- Event handlers. (Button touches, swipe handlers, etc.)
- Delegate methods.
- Helpers only used in this file.

Put at least one empty line between blocks, @ directives, etc. Exceptions:
- No space between a pragma mark and the first method in that group. That makes it a bit easier to see what methods belong together when collapsing all methods.
- No space between a comment and the code it documents.

Method signatures should have the format:
<-/+><space><type><name><parameters><starting bracket>
*/
@implementation MyClass

#pragma mark View controller overrides
- (void)viewDidLoad{
  [super viewDidLoad];
}

#pragma mark Public interface
- (void)play{
}

- (void)pause{
}

#pragma mark Event handlers
- (IBAction)buttonTouchUp:(id)sender{
}

#pragma mark UIViewControllerTransitioningDelegate
- (id<UIViewControllerAnimatedTransitioning>)animationControllerForPresentedController:(UIViewController *)presented presentingController:(UIViewController *)presenting sourceController:(UIViewController *)source{
    return [MyOtherClass new];
}

#pragma mark Helpers
- (float)veryComplexCalculation:(float)x{
  return x + 1.0f;
}

@end
/*
End file with an empty line.
*/

```
