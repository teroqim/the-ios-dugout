Navigation bars
---------------

Hide drop shadow
```objective-c
[self.navigationController.navigationBar setBackgroundImage:[UIImage new]
                                          forBarPosition:UIBarPositionAny
                                          barMetrics:UIBarMetricsDefault];
[self.navigationController.navigationBar setShadowImage:[UIImage new]];
```

Setup button
```objective-c
UIButton *settingsButton = [[UIButton alloc] initWithFrame:CGRectMake(0, 0, 60.0f, self.navigationController.navigationBar.frame.size.height)];
UIImage *settingsImage = [[UIImage imageNamed:@"settings-icn"] imageWithRenderingMode:UIImageRenderingModeAlwaysTemplate];
[settingsButton setImage:settingsImage  forState:UIControlStateNormal];
settingsButton.contentMode = UIViewContentModeCenter;
[settingsButton setTitle:@"" forState:UIControlStateNormal];
[settingsButton addTarget:self action:@selector(showSettings) forControlEvents:UIControlEventTouchUpInside];
[settingsButton addTarget:self action:@selector(highlightSettingsButton) forControlEvents:UIControlEventTouchDown];
[settingsButton addTarget:self action:@selector(unhighlightSettingsButton) forControlEvents:UIControlEventTouchUpOutside];
settingsButton.adjustsImageWhenHighlighted = NO;
settingsButton.tintColor = [UIColor whiteColor];
settingsButton.layoutMargins = UIEdgeInsetsZero;
self.settingsButton = settingsButton;
UIView *settingsButtonView = [[UIView alloc] initWithFrame:settingsButton.bounds];
self.settingsButton.imageEdgeInsets = UIEdgeInsetsMake(0, 18, 0, -18);
[settingsButtonView addSubview:settingsButton];
UIBarButtonItem *settingsButtonItem = [[UIBarButtonItem alloc] initWithCustomView:self.settingsButton];
self.navigationItem.rightBarButtonItem = settingsButtonItem;
```

Hide navigation bar
```objective-c
[self.navigationController setNavigationBarHidden:YES animated:NO];

Set title text
```objective-c
//Set font attributes for label text
UILabel *titleLabel = [UILabel new];
UIFont *font = [UIFont fontWithName:@"AvenirNext-Bold" size:12];
if ([self.podcast.title length] > 24) {
    titleLabel.numberOfLines = 0;
    font = [UIFont fontWithName:@"AvenirNext-Bold" size:10];
}
NSAttributedString *attributedString =
[[NSAttributedString alloc]
 initWithString:[self.podcast.title uppercaseString]
 attributes:
 @{
   NSFontAttributeName : font,
   NSForegroundColorAttributeName : [UIColor whiteColor],
   NSKernAttributeName : @(0.5f)
   }];
titleLabel.attributedText = attributedString;
[titleLabel sizeToFit];
CGRect frame = titleLabel.frame;
frame.size.height = 2*frame.size.height;
titleLabel.frame = frame;
titleLabel.textAlignment = NSTextAlignmentCenter;
self.navigationItem.titleView = titleLabel;
```

Setup back button
```objective-c
//Create back button
UIButton *backButton = [[UIButton alloc] initWithFrame:CGRectMake(0, 0, 60.0f, self.navigationController.navigationBar.frame.size.height)];
UIImage *backImage = [[UIImage imageNamed:@"back-icn"] imageWithRenderingMode:UIImageRenderingModeAlwaysTemplate];
[backButton setImage:backImage  forState:UIControlStateNormal];
backButton.contentMode = UIViewContentModeCenter;
[backButton setTitle:@"" forState:UIControlStateNormal];
[backButton addTarget:self action:@selector(unwind) forControlEvents:UIControlEventTouchUpInside];
[backButton addTarget:self action:@selector(highlightBackButton) forControlEvents:UIControlEventTouchDown];
[backButton addTarget:self action:@selector(unhighlightBackButton) forControlEvents:UIControlEventTouchUpOutside];
backButton.adjustsImageWhenHighlighted = NO;
backButton.tintColor = [UIColor whiteColor];
backButton.layoutMargins = UIEdgeInsetsZero;
self.backButton = backButton;
//Setting the button as custom view to the UIBarButtonItem has the unwanted effect
//of changing the touch area of the button to be too big.
//Using a view as a intermediate layer is a workaround to that.
UIView *backButtonView = [[UIView alloc] initWithFrame:backButton.bounds];
//These offsets are set by inspection to center the image. Should perhaps use constraints instead.
backButtonView.bounds = CGRectOffset(backButtonView.bounds, 18, 8);
[backButtonView addSubview:backButton];
//back button item too wide..
UIBarButtonItem *backButtonItem = [[UIBarButtonItem alloc] initWithCustomView:backButtonView];
self.navigationItem.leftBarButtonItem = backButtonItem;
```
