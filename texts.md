Texts
=====
Anything about texts.

Attributed texts
----------------
To fully customize texts you create an attributed string which you can then assign to a label, button, etc.

How to create an attributed string:
```objective-c
NSMutableParagraphStyle *style = [NSMutableParagraphStyle new];
style.lineBreakMode = NSLineBreakByWordWrapping;
style.alignment = NSTextAlignmentRight;
UIFont *font = [UIFont fontWithName:@"AvenirNext-DemiBold" size:8];
NSAttributedString *attributedString =
    [[NSAttributedString alloc]
     initWithString:@"Some text to set attributes for"
     attributes:
     @{
       NSFontAttributeName : font,
       NSForegroundColorAttributeName : [UIColor whiteColor],
       NSKernAttributeName : @(1.0f), //This is the space between characters
       NSParagraphStyleAttributeName: style
       }];
```
How to assign the attributed string:
```objective-c
//Label
titleLabel.attributedText = attributedString;

//Button
[myButton setAttributedTitle:attributedString forState:UIControlStateHighlighted];
```

It is also posible to concatenate multiple attributed strings with different attributes into one by using NSMutableAttributedString
