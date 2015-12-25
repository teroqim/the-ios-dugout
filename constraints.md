Constraints
-----------
NOTE: This is preliminary, needs more work. This is an example of setting constraints programmatically taken from a project of mine.

```objective-c
self.downloadImageView = [UIImageView new];
    [self updateDownloadIcon];
    self.downloadImageView.tintColor = [UIColor whiteColor];
    self.downloadImageView.contentMode = UIViewContentModeCenter;
    self.downloadImageView.backgroundColor = [UIColor clearColor];
    
    self.downloadView = [UIView new];
    [self.downloadView addSubview:self.downloadImageView];
    [self addSubview:self.downloadView];
    [self sendSubviewToBack:self.downloadView];
    self.downloadView.layer.masksToBounds = YES;
    [self setDownloadViewOriginalColor:0];
    
    self.downloadImageView.translatesAutoresizingMaskIntoConstraints = NO;
    self.downloadView.translatesAutoresizingMaskIntoConstraints = NO;
    
    NSDictionary *viewsDictionary = @{@"imgView":self.downloadImageView,
                                      @"downloadView": self.downloadView,
                                      @"superview": self};
    //Dimensions for image
    [self.downloadImageView addConstraints:[NSLayoutConstraint constraintsWithVisualFormat:@"H:[imgView(24)]"
                                                                    options:0
                                                                    metrics:nil
                                                                      views:viewsDictionary]];
    
    [self.downloadImageView addConstraints:[NSLayoutConstraint constraintsWithVisualFormat:@"V:[imgView(24)]"
                                                                    options:0
                                                                    metrics:nil
                                                                      views:viewsDictionary]];
    //Placement image
    // Center horizontally
    [self.downloadView addConstraints:[NSLayoutConstraint constraintsWithVisualFormat:@"V:[downloadView]-(<=1)-[imgView]"
                                                                              options:NSLayoutFormatAlignAllCenterX
                                                                              metrics:nil
                                                                                views:viewsDictionary]];
    
    
    // Center vertically
    [self.downloadView addConstraints:[NSLayoutConstraint constraintsWithVisualFormat:@"H:[downloadView]-(<=1)-[imgView]"
                                                                              options:NSLayoutFormatAlignAllCenterY
                                                                              metrics:nil
                                                                                views:viewsDictionary]];
    
    //Dimensions download view
    self.downloadViewWidthLayoutConstraint = [NSLayoutConstraint constraintWithItem:self.downloadView attribute:NSLayoutAttributeWidth relatedBy:NSLayoutRelationEqual toItem:nil attribute:NSLayoutAttributeNotAnAttribute multiplier:1 constant:0];

    [self.downloadView addConstraint:self.downloadViewWidthLayoutConstraint];
    
    [self addConstraint: [NSLayoutConstraint constraintWithItem:self.downloadView
                                                      attribute:NSLayoutAttributeHeight
                                                      relatedBy:NSLayoutRelationEqual
                                                         toItem:self
                                                      attribute:NSLayoutAttributeHeight
                                                     multiplier:1
                                                       constant:0]];
    
    //Placement download view
    [self addConstraint: [NSLayoutConstraint constraintWithItem:self.downloadView
                                                      attribute:NSLayoutAttributeRight
                                                      relatedBy:NSLayoutRelationEqual
                                                         toItem:self
                                                      attribute:NSLayoutAttributeLeft
                                                     multiplier:1
                                                       constant:0]];
    
    [self addConstraint: [NSLayoutConstraint constraintWithItem:self.downloadView
                                                                  attribute:NSLayoutAttributeTop
                                                                  relatedBy:NSLayoutRelationEqual
                                                                     toItem:self
                                                                  attribute:NSLayoutAttributeTop
                                                                 multiplier:1
                                                                   constant:0]];
```
