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
UIBarButtonItem *backButtonItem = [[UIBarButtonItem alloc] initWithCustomView:backButton];  
self.navigationItem.leftBarButtonItem = backButtonItem;
```

Dividers
--------
Here's how to add a bottom separator to a tabbar for the times when you have a tabbar anywhere but the at the bottom.
```objective-c
// Create a new layer which is the width of the device and with a heigh
// of 0.5px.
CALayer *bottomBorder = [CALayer layer];
bottomBorder.frame = CGRectMake(0.0f, self.tabBar.frame.size.height-0.5f, self.view.frame.size.width, 0.5f);
bottomBorder.backgroundColor = [[UIColor colorWithRed:179.0/255 green:179.0/255 blue:179.0/255 alpha:0.6] CGColor];
[self.tabBar.layer addSublayer:bottomBorder];
self.tabBar.clipsToBounds = YES;
```

Here's how to add a vertical separator at the middle of the tabbar
```objective-c
CALayer *middleDivider = [CALayer layer];
middleDivider.frame = CGRectMake(self.tabBar.frame.size.width/2-0.5f, 10, 1.0f, self.tabBar.frame.size.height-20);
middleDivider.backgroundColor = [[UIColor colorWithRed:179.0/255 green:179.0/255 blue:179.0/255 alpha:0.25] CGColor];
[self.tabBar.layer addSublayer:middleDivider];
```

Global attributes
-----------------
To set attributes that apply to all instances of a navigationbar/tabbar/etc use the static attribute 'appearance' like so:
```objective-c
UIFont *font = [UIFont fontWithName:@"AvenirNext-DemiBold" size:10];
[[UITabBarItem appearance] setTitleTextAttributes:@{
                                                   NSFontAttributeName : font,
                                                   NSForegroundColorAttributeName : [UIColor blackColor],
                                                   NSKernAttributeName : @(1.0f)
                                                   } forState:UIControlStateNormal];

[[UITabBarItem appearance] setTitleTextAttributes:@{
                                                    NSFontAttributeName : font,
                                                    NSForegroundColorAttributeName : self.podcast.color,
                                                    NSKernAttributeName : @(1.0f)
                                                    } forState:UIControlStateSelected];
    
[[UITabBar appearance] setShadowImage:[[UIImage alloc] init]];
    
CGRect screenSize = [[UIScreen mainScreen] applicationFrame];
NSUInteger count  = [self.viewControllers count];
float width  = screenSize.size.width/count;
[[UITabBar appearance] setItemWidth:width];
    
[[UITabBarItem appearance] setTitlePositionAdjustment:UIOffsetMake(0, -15)];
```
These settings could for instance be put in the AppDelegate.

TabBar on top
-------------
How to place the tabbar just below a navigation bar instead of at the bottom.
```objective-c
CGRect fn = self.navigationController.navigationBar.frame;
CGRect f = self.tabBar.frame;
f.origin.y = fn.size.height;
[self.tabBar setFrame:f];
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
and here is how to set a background image for the navigation bar
```objective-c
[self.navigationController.navigationBar setBackgroundImage:image forBarMetrics:UIBarMetricsDefault];
```
How to hide the navigation bar:
```objective-c
[self.navigationController setNavigationBarHidden:NO animated:YES];
```
To change the title label of navigation bar
```objective-c
//Set up label first, then assign
[titleLabel sizeToFit];
self.navigationItem.titleView = titleLabel;
```
