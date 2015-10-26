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

Max number of lines in text container
====================================
```objective-c
- (BOOL)textView:(UITextView *)textView shouldChangeTextInRange:(NSRange)range replacementText:(NSString *)text{
    NSMutableString *t = [NSMutableString stringWithString: textView.text];
    [t replaceCharactersInRange: range withString: text];
    
    NSUInteger numberOfLines = 1;
    for (NSUInteger i = 0; i < t.length; i++) {
        if ([[NSCharacterSet newlineCharacterSet] characterIsMember: [t characterAtIndex: i]]) {
            numberOfLines++;
        }
    }

    //Now check for word wrapping onto newline.
    NSAttributedString *t2 = [[NSAttributedString alloc]
                                  initWithString:[NSMutableString stringWithString:t] attributes:@{NSFontAttributeName:textView.font}];
        
    __block NSInteger lineCount = 0;
        
    CGFloat maxWidth   = textView.textContainer.size.width;
    NSLog(@"maxWidth = %.02f", maxWidth);
        
    NSTextContainer *tc = [[NSTextContainer alloc] initWithSize:CGSizeMake(maxWidth, CGFLOAT_MAX)];
    NSLayoutManager *lm = [[NSLayoutManager alloc] init];
    NSTextStorage   *ts = [[NSTextStorage alloc] initWithAttributedString:t2];
    [ts addLayoutManager:lm];
    [lm addTextContainer:tc];
    [lm enumerateLineFragmentsForGlyphRange:NSMakeRange(0, lm.numberOfGlyphs)
                                     usingBlock:^(CGRect rect,
                                                  CGRect usedRect,
                                                  NSTextContainer *textContainer,
                                                  NSRange glyphRange,
                                                  BOOL *stop)
     {
         lineCount++;
     }];
        
    return (lineCount <= textView.textContainer.maximumNumberOfLines);
}
```
