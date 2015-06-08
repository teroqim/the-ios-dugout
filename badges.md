Badges
======

Examples of creating custom badges.

Tab bar item
------------
A subclass of UITabBarItem with the possibility to set a simple badge.

Header-file:
```objective-c
#import <UIKit/UIKit.h>


@interface TTabBarItem : UITabBarItem

- (void)setCustomBadge;
- (void)bumpBadge;
- (void)clearBadge;

@end

```

Implementation:
```objective-c
#import "TTabBarItem.h"

#define CUSTOM_BADGE_TAG 99


@interface TTabBarItem ()

@property BOOL hasBadge;
@property UIView *badgeView;

@end


@implementation TTabBarItem

- (void)setCustomBadge{
    if (self.hasBadge){
        return;
    }
    
    UIView *v = [self valueForKey:@"view"];
    [self setBadgeValue:@""];
    
    for(UIView *sv in v.subviews)
    {
        NSString *str = NSStringFromClass([sv class]);
        if([str isEqualToString:@"_UIBadgeView"])
        {
            for(UIView *ssv in sv.subviews){
                [ssv removeFromSuperview];
            }
            CGRect rect = sv.bounds;
            rect.origin.y += 5;
            rect.size.width = sv.frame.size.width/2;
            rect.size.height = sv.frame.size.width/2;
            UIView *v = [[UIView alloc] initWithFrame:rect];
            sv.backgroundColor = [UIColor clearColor];
            v.backgroundColor = [UIColor redColor];
            v.layer.cornerRadius = sv.frame.size.width/4;
            v.layer.masksToBounds = YES;
            [sv addSubview:v];
            self.badgeView = v;
        }
    }
    self.hasBadge = YES;
}

- (void)bumpBadge{
    [UIView animateWithDuration:0.1 animations:^{
        self.badgeView.transform = CGAffineTransformMakeScale(1.5, 1.5);
    } completion:^(BOOL finished){
        [UIView animateWithDuration:0.2 animations:^{
            self.badgeView.transform = CGAffineTransformIdentity;
        } completion:^(BOOL finished){
        }];
    }];
}

- (void)clearBadge{
    self.hasBadge = NO;
    if (self.badgeView){
        [self.badgeView removeFromSuperview];
        self.badgeView = nil;
    }
}

@end

```
