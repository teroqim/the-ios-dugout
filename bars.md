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
