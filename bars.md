Bars
====

Navigation bars don't always work the way we want them to. Here are some examples of workarounds.

Height
------
It's incredible that it's so hard to set the height of a UINavigationBar or a UITabBar. The examples below shows ways around this.

NavigationBar:
```objective-c

#import <UIKit/UIKit.h>


@interface TNavigationBar : UINavigationBar

@end

```
```objective-c

#import "TNavigationBar.h"


@implementation TNavigationBar

- (id)initWithCoder:(NSCoder *)aDecoder{
    if(self = [super initWithCoder:aDecoder]){
        self.translucent = NO;
    }
    return self;
}

- (void)layoutSubviews{
    [super layoutSubviews];
    
    NSArray *subviews = self.subviews;
    for (UIView *view in subviews) {
        if ([view isKindOfClass:[UIButton class]] || [view isKindOfClass:[UILabel class]]) {
            view.frame = ({
                CGRect frame = view.frame;
                CGFloat navigationBarHeight = CGRectGetHeight(self.frame);
                CGFloat buttonHeight = CGRectGetHeight(view.frame);
                frame.origin.y = (navigationBarHeight - buttonHeight) / 2.0f;
                frame;
            });
        }
    }
}

- (CGSize)sizeThatFits:(CGSize)size{
    CGSize sizeThatFits = [super sizeThatFits:size];
    sizeThatFits.height = 60;
    
    return sizeThatFits;
}

@end
```

TabBar:
```objective-c

#import <UIKit/UIKit.h>


@interface TTabBar : UITabBar

@end

```
```objective-c

#import "TTabBar.h"


@implementation TTabBar

- (CGSize)sizeThatFits:(CGSize)size{
    CGSize sizeThatFits = [super sizeThatFits:size];
    sizeThatFits.height = 50;
    
    return sizeThatFits;
}

@end

```

Buttons
-------
Here is one way to set a custom back button on a navigation bar:
```objective-c
UIButton *backButton = [[UIButton alloc] initWithFrame: CGRectMake(0, 0, 20.0f, 16.0f)];
UIImage *backImage = [[UIImage imageNamed:@"back-arrow-icn"] imageWithRenderingMode:UIImageRenderingModeAlwaysTemplate];
[backButton setBackgroundImage:backImage  forState:UIControlStateNormal];
[backButton setTitle:@"" forState:UIControlStateNormal];
[backButton addTarget:self action:@selector(unwind) forControlEvents:UIControlEventTouchUpInside];
UIBarButtonItem *backButtonItem = [[UIBarButtonItem alloc] initWithCustomView:backButton];    self.navigationItem.leftBarButtonItem = backButtonItem;
```

Misc
----
A couple of times I've had problems with the title in a navigation bar being misplaced vertically. Either permanently or for instance during segue animations. One work around for this have been to manually counter displace the title. Not an ideal solution and I should definitely dig into what the problem really is, but until then...
```objective-c
//This is here because of a graphical bug, where the navigation bar title
//is misplaced during segue animation. Got to be a better way to fix this..
[self.navigationBar setTitleVerticalPositionAdjustment:-8.0f forBarMetrics:UIBarMetricsDefault];
```

You'd think it would be easy to set the background color of the navigation bar. Well, it is, but it's not like setting the background color of any other view, instead you need set the property barTintColor like so:
```objective-c
self.navigationBar.barTintColor = [UIColor blackColor];
```
