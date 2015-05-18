Blurring
========
Different ways to blur images/views. Note that there are noticeable performance/options trade offs between these alternatives.
Blurring an image
-----------------
Install GPUImage: https://github.com/BradLarson/GPUImage  
Then use/modify the following code to your liking:
```objective-c
#import <GPUImage.h>

@implementation MyClass

- (UIImage *)blurryGPUImage:(UIImage *)image{
    GPUImageiOSBlurFilter *filter = [[GPUImageiOSBlurFilter alloc] init];
    filter.downsampling = 3.0;
    filter.saturation = 1.5;
    return [filter imageByFilteringImage:image];
}

@end

```

Blurring a view
----------------------
```objective-c
- (void)blurView:(UIView *)viewToBlur{
      UIBlurEffect *blurEffect = [UIBlurEffect effectWithStyle:UIBlurEffectStyleDark];
      UIVisualEffectView *blurView = [[UIVisualEffectView alloc] initWithEffect:blurEffect];
      [blurView setFrame:viewToBlur.bounds];
      [viewToBlur addSubview:blurView];
 }
```
