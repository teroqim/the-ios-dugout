Visualizations
==============

Audio
-----
Here is a class that used to be both an audio player and a view with a custom visualization if the data.

[insert images of visualization here]

This needs to be cleaned up and separated into a component for the player and one for the visualization.
```objective-c

#import <UIKit/UIKit.h>
#import <AVFoundation/AVFoundation.h>
#import <CoreText/CoreText.h>


@class TAudioPlayer;

@protocol TAudioPlayerDelegate <NSObject>

@optional -(void)audioTrackFinished;
@optional -(void)startedPlayback;
@optional -(void)stoppedPlayback;

@end


@interface TAudioPlayer : UIControl <AVAudioPlayerDelegate>

@property id <TAudioPlayerDelegate> delegate;
@property BOOL shouldTrackTouch;
@property BOOL shouldShowTime;

- (void)setAudioFile:(NSString *)audioFileURL;
- (void)play;
- (void)stop;
- (void)pause;
- (void)clear;
- (void)refresh;
- (NSTimeInterval) getDuration;
- (BOOL)isPlaying;
- (NSInteger)currentTime;
- (void)setCurrentTime:(NSTimeInterval)time;

@end

```
```objective-c

#import "TAudioPlayer.h"

#import <stdlib.h>


@interface TAudioPlayer ()

@property NSArray *sampleData;
@property NSString *fileURL;
@property NSTimer *timer;

//For the view
@property CGFloat widthPercentage;
@property CGFloat lineWidth;

//Player
@property AVAudioPlayer *player;

@end


@implementation TAudioPlayer

- (id)initWithFrame:(CGRect)frame{
    self = [super initWithFrame:frame];
    [self setBackgroundColor: [UIColor colorWithRed:0 green:0 blue:0 alpha:0]];
    self.shouldTrackTouch = NO;
    self.shouldShowTime = YES;
    UILongPressGestureRecognizer *recognizer = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(respondToPressGesture:)];
    [self addGestureRecognizer:recognizer];

    return self;
}

- (NSTimeInterval) getDuration{
    return self.player.duration;
}

- (BOOL)beginTrackingWithTouch:(UITouch *)touch withEvent:(UIEvent *)event{
    NSLog(@"began track");
    if (self.shouldTrackTouch){
        CGPoint point = [touch locationInView:self];
        [self drawPercentageOfWidth:point.x/self.bounds.size.width];
        [self setCurrentTime:(point.x/self.bounds.size.width)*self.player.duration];
    }
    else{
        [self play];
    }
    
    [self sendActionsForControlEvents:UIControlEventTouchDown];
    return YES;
}

- (BOOL)continueTrackingWithTouch:(UITouch *)touch withEvent:(UIEvent *)event{
    NSLog(@"moved");
    if (self.shouldTrackTouch){
        CGPoint point = [touch locationInView:self];
        [self drawPercentageOfWidth:point.x/self.bounds.size.width];
        [self setCurrentTime:(point.x/self.bounds.size.width)*self.player.duration];
    }
    return YES;
}

- (void)endTrackingWithTouch:(UITouch *)touch withEvent:(UIEvent *)event{
    NSLog(@"end tracking");
    CGPoint point = [touch locationInView:self];
    
    if(CGRectContainsPoint(self.frame, point)){
        [self sendActionsForControlEvents:UIControlEventTouchUpInside];
    }
    else{
        [self sendActionsForControlEvents:UIControlEventTouchUpOutside];
    }
    
    if (self.shouldTrackTouch){
        CGPoint point = [touch locationInView:self];
        [self drawPercentageOfWidth:point.x/self.bounds.size.width];
        [self setCurrentTime:(point.x/self.bounds.size.width)*self.player.duration];
    }
    else{
        [self stop];
    }
    
}

- (void)cancelTrackingWithEvent:(UIEvent *)event{
    NSLog(@"cancel tracking");
    if (self.shouldTrackTouch){
    }
    else{
//        [self stop];
    }
    
//    [self sendActionsForControlEvents:UIControlEventTouchCancel];
}


- (void)setAudioFile:(NSString *)audioFileURL{
    if (!self.fileURL || ![self.fileURL isEqualToString:audioFileURL]){
//        NSLog(@"setting new audio file %@", audioFileURL);
        //NOTE: Possibly inefficitent and stupid to do this here, but it works for now
        //If we want to stream audio data this should be updated as new data comes in
        //If we provide converted data as meta data to sources this is probably unnecessary...
        self.fileURL = audioFileURL;
        [self createNewAVAudioPlayer:audioFileURL];
//        FIOREPlayerDataConverter *converter = [[FIOREPlayerDataConverter alloc] initWithAudioFileURL:audioFileURL];
//        self.sampleData = converter.stepValues;
    }
}

- (void)refresh{
    [self updateProgressBar:nil];
}

- (void)setCurrentTime:(NSTimeInterval)time{
    [self.player setCurrentTime:time];
    [self setNeedsDisplayInRect:self.frame];
}

- (NSTimeInterval)currentTime{
    return self.player.currentTime;
}

- (BOOL)isPlaying{
    return self.player.playing;
}

- (void)play{
    if (!self.fileURL){
        NSLog(@"no fileurl");
        return;
    }
    
    if (!self.player){
        [self createNewAVAudioPlayer:self.fileURL];
    }
    
    [self.player play];
    
    //Start timer that will update view
    self.timer = [NSTimer scheduledTimerWithTimeInterval:0.01 target:self selector:@selector(updateProgressBar:) userInfo:nil repeats:YES];
    
    if (self.delegate && [self.delegate respondsToSelector:@selector(startedPlayback)]){
        [self.delegate startedPlayback];
    }
}

- (void)createNewAVAudioPlayer:(NSString *)URL{
    NSString *soundFilePath =
    [[NSBundle mainBundle] pathForResource: URL
                                    ofType: @"mp3"];
    NSURL *fileURL = [[NSURL alloc] initFileURLWithPath: soundFilePath];
    
    self.player =
    [[AVAudioPlayer alloc] initWithContentsOfURL:fileURL error: nil];
    self.player.delegate = self;
    
//    self.duration = self.player.duration;
}

- (void)updateProgressBar:(NSTimer *)timer{
    NSNumber *num = [[NSNumber alloc] initWithDouble:self.player.currentTime / self.player.duration];
    [self drawPercentageOfWidth:num.floatValue];
}

- (void)stop{
    if (!self.player){
        return;
    }
//    NSLog(@"Start play");
    
    [self.player stop];
    self.player.currentTime = 0;
    [self.timer invalidate];
    [self drawPercentageOfWidth:0];
}

- (void)pause{
    [self.player pause];
}

- (NSString *)getTimeString:(NSTimeInterval)interval{
    int i = (int) floor(interval);
    int mins = i / 60;
    int secs = i % 60;
    return [NSString stringWithFormat:@"%d:%02d", mins, secs];
}

- (void)drawRect:(CGRect)rect {
    
//    NSLog(@"Draw: %f, %f", rect.size.width, rect.size.height);
    
    if (!self.sampleData){
        NSLog(@"No sample data for drawing");
        return;
    }
    
    CGContextRef graphicsContext = UIGraphicsGetCurrentContext();
    
    //turn coordinate system upside down (Make lower left origin, positive x right, positive y up)
    CGContextScaleCTM(graphicsContext, 1, -1);
    CGContextTranslateCTM(graphicsContext, 0, -rect.size.height);
    
    //Translate y origin to middle of rect
    CGContextTranslateCTM(graphicsContext, 0, rect.size.height/2);
    
    //Should be value between 0 and 1
    CGFloat progressPercent = 0;
    if (self.widthPercentage){
        progressPercent = MIN(MAX(self.widthPercentage, 0), 1);
    }
    NSInteger nrSteps = [self.sampleData count];
    self.lineWidth = rect.size.width/nrSteps;
    int nrRed = (int) floor(progressPercent * nrSteps);
    CGFloat rest = progressPercent*nrSteps - nrRed;
    //NSLog(@"steps %i, nrred %i pro %f, pro*nrsteps %f, rest %f", nrSteps, nrRed, progressPercent, progressPercent*nrSteps, rest);
    CGFloat xBreak = nrRed*(rect.size.width/nrSteps);
    CGRect rectRed = CGRectMake(0, 0, xBreak, rect.size.height);
    CGRect rectWhite = CGRectMake(xBreak, 0, rect.size.width-xBreak, rect.size.height);
    
    //NSLog(@"nrRed: %i, rect.size.width/nrSteps: %f, xBreak: %f", nrRed, rect.size.width/nrSteps, xBreak);
    
    //White top
    [self drawProgressBar:graphicsContext rect:rectWhite offset:nrRed nrSteps:nrSteps-nrRed rest:0 color:[UIColor whiteColor]];
    
    //White bottom
    CGContextScaleCTM(graphicsContext, 1, -1);
    [self drawProgressBar:graphicsContext rect:rectWhite offset:nrRed nrSteps:nrSteps-nrRed rest:0 color:[UIColor colorWithRed:1 green:1 blue:1 alpha:0.4]];
    
    if (xBreak > 0){
        //Draw red
        //Red top
        CGContextScaleCTM(graphicsContext, 1, -1);
        [self drawProgressBar:graphicsContext rect:rectRed offset:0 nrSteps:nrRed rest:rest color:[UIColor redColor]];
        
        //Red bottom
        CGContextScaleCTM(graphicsContext, 1, -1);
        [self drawProgressBar:graphicsContext rect:rectRed offset:0 nrSteps:nrRed rest:rest color:[UIColor colorWithRed:1 green:0 blue:0 alpha:0.4]];
    }
    
    if (self.shouldShowTime){
        //Draw time text
        CGContextScaleCTM(graphicsContext, 1, -1);
        //played time
        CGFloat fontSize = ceil(rect.size.height/3);
        UIColor *c = [UIColor redColor];
        CGRect tRect = CGRectMake(rect.size.width/50, -1.5*fontSize, rect.size.width - 2*rect.size.width/50, rect.size.height/2);
        [self drawText:graphicsContext inRect:tRect forTime:[self getTimeString:self.player.currentTime] height:fontSize color:c rightAligned:NO];
        
        //Full time
        c = [UIColor whiteColor];
        [self drawText:graphicsContext inRect:tRect forTime:[self getTimeString:self.player.duration] height:fontSize color:c rightAligned:YES];
    }
}

- (void)drawText:(CGContextRef)context inRect:(CGRect)rect forTime:(NSString *)timeString height:(CGFloat)height color:(UIColor *)color rightAligned:(BOOL)isRightAligned{
    
//    NSLog(@"Text: %f, %f", rect.size.width, rect.size.height);
    
    CTTextAlignment alignment = kCTLeftTextAlignment;
    if (isRightAligned){
        alignment = kCTRightTextAlignment;
    }
    CTParagraphStyleSetting settings[] = {
        {kCTParagraphStyleSpecifierAlignment, sizeof(alignment), &alignment}
    };
    CTParagraphStyleRef paragraphStyle = CTParagraphStyleCreate(settings, sizeof(settings) / sizeof(settings[0]));
    
    NSDictionary *fontAttributes =
    [NSDictionary dictionaryWithObjectsAndKeys:
     @"Helvetica neue", (id)kCTFontFamilyNameAttribute,
     @"Bold", (id)kCTFontStyleNameAttribute,
     [NSNumber numberWithFloat:height],(id)kCTFontSizeAttribute,
     color.CGColor, (id)kCTForegroundColorAttributeName,
     paragraphStyle, (id)kCTParagraphStyleAttributeName,
     nil];
    
    NSAttributedString *stringToDraw = [[NSAttributedString alloc] initWithString:timeString attributes:fontAttributes];
    
    CGContextSetTextMatrix(context, CGAffineTransformIdentity);
    
    //CTLineRef line = CTLineCreateWithAttributedString((CFAttributedStringRef)stringToDraw);
    
//    CGContextSetTextPosition(context, 0, 0);
//    CTLineDraw(line, context);
    CTFramesetterRef framesetter = CTFramesetterCreateWithAttributedString((CFAttributedStringRef)stringToDraw);
    
    // Create the Core Text frame using our current view rect bounds.
    UIBezierPath *path = [UIBezierPath bezierPathWithRect:rect];
    CTFrameRef frame =  CTFramesetterCreateFrame(framesetter, CFRangeMake(0, 0), [path CGPath], NULL);
    CTFrameDraw(frame, context);
}

//Privates
- (void)drawPercentageOfWidth:(CGFloat)percent{
    self.widthPercentage = percent;
    [self setNeedsDisplayInRect:self.bounds];
}

- (void)drawProgressBar:(CGContextRef)context rect:(CGRect)rect offset:(NSInteger)offset nrSteps:(NSInteger)nrSteps rest:(CGFloat)rest color:(UIColor*)color{
    
    CGFloat lineWidth = rect.size.width/nrSteps;
    CGContextSetLineWidth(context, lineWidth - 0.5);
    CGFloat r=1, g=1, b=1, a=1;
    [color getRed:&r green:&g blue:&b alpha:&a];
    CGContextSetRGBStrokeColor(context, r, g, b, a);
    
    //Draw all staples except any unfinished staple
    CGContextBeginPath(context);
    CGContextMoveToPoint(context,0, 0);
    for (int i = 0; i < nrSteps; i++){
        float f = i/(float)nrSteps;
//        NSLog(@"offset+i: %li", offset+i);
        NSNumber *n = (NSNumber *) [self.sampleData objectAtIndex:offset+i];
//        if (n.floatValue < 0.05){
//            continue;
//        }
//        NSLog(@"stapelX: %f, %f, %f", rect.origin.x + f*rect.size.width, rect.origin.x, f*rect.size.width);
        CGContextMoveToPoint(context,lineWidth/2 + rect.origin.x + f*rect.size.width, 0.4);
        CGContextAddLineToPoint(context, lineWidth/2 + rect.origin.x + f*rect.size.width, n.floatValue*rect.size.height/2);
    }
    CGContextClosePath(context);
    
    CGContextAddPath(context, nil);
    CGContextDrawPath(context, kCGPathStroke);
    
    
    if (rest > 0.05){
        NSNumber *n = (NSNumber *) [self.sampleData objectAtIndex:offset+nrSteps];
        CGFloat height = rest*n.floatValue*rect.size.height/2;
        if (height > 0.4){
            CGContextSaveGState(context);
            
            //Clear out old transparent color, if any
            CGContextSetBlendMode(context, kCGBlendModeClear);
            CGContextBeginPath(context);
            CGContextMoveToPoint(context,0, 0);
            CGContextMoveToPoint(context, lineWidth/2 + rect.origin.x + rect.size.width, 0.4);
            CGContextAddLineToPoint(context, lineWidth/2 + rect.origin.x + rect.size.width, height);
            CGContextClosePath(context);
            CGContextAddPath(context, nil);
            CGContextDrawPath(context, kCGPathStroke);
            CGContextRestoreGState(context);
            
            //Draw rest stack
            CGContextBeginPath(context);
            CGContextMoveToPoint(context,0, 0);
            CGContextMoveToPoint(context, lineWidth/2 + rect.origin.x + rect.size.width, 0.4);
            CGContextAddLineToPoint(context, lineWidth/2 + rect.origin.x + rect.size.width, height);
            CGContextClosePath(context);
            CGContextAddPath(context, nil);
            CGContextDrawPath(context, kCGPathStroke);
        }
        
    }
}

@end
```

Here an animation for audio, although it is not tied any audio file in particular.

[Images of animation here]

```objective-c

#import <UIKit/UIKit.h>


@interface TPlayAnimationView : UIView

@property UIColor *barColor;
@property NSInteger nrBars;
@property BOOL mirrorBars;
@property float minValue;
@property float maxValue;
@property float speedFactor;

- (void)resetAnimation;
- (void)startAnimation;
- (void)stopAnimation;

@end

```

```objective-c

#import "TPlayAnimationView.h"
#import <stdlib.h>


@interface TPlayAnimationView ()

@property NSMutableArray *currentValues;
@property NSMutableArray *currentDirections;
@property NSMutableArray *speeds;
@property NSTimer *timer;

@end


@implementation TPlayAnimationView

- (id)initWithFrame:(CGRect)frame{
    self = [super initWithFrame:frame];
    
    //Set default values
    [self setBackgroundColor: [UIColor colorWithRed:0 green:0 blue:0 alpha:0]];
    self.nrBars = 16;
    self.barColor = [UIColor redColor];
    self.minValue = 0.10;
    self.maxValue = 0.25;
    self.mirrorBars = NO;
    self.speedFactor = 1.0f;
    
    //Init and start animation
    [self resetAnimation];
    return self;
}

- (id)hitTest:(CGPoint)point withEvent:(UIEvent *)event{
    UIView *hitView = [super hitTest:point withEvent:event];
    if (hitView == self){
        return nil;
    }
    else {
        return hitView;
    }
}

- (void)resetAnimation{
    self.currentValues = [[NSMutableArray alloc] init];
    self.currentDirections = [[NSMutableArray alloc] init];
    self.speeds = [[NSMutableArray alloc] init];
    for (int i = 0; i < self.nrBars; i++){
        //Gen number between -0.5 and 1.0
        int u = arc4random_uniform(100);
        float f = u*(self.maxValue - self.minValue)/100 + self.minValue;
        NSNumber *r = [[NSNumber alloc] initWithFloat:f];
        [self.currentValues addObject:r];
        
        NSNumber *j = [[NSNumber alloc] initWithInteger: arc4random_uniform(1)*2 - 1];
        [self.currentDirections addObject:j];
        
        //Init speeds
        CGFloat screenHeight = ([[UIScreen mainScreen] bounds]).size.height;
        CGFloat scale = self.frame.size.height/screenHeight;
        NSNumber *k = [[NSNumber alloc] initWithFloat: (arc4random_uniform(100)*0.2/100 + 0.1)*scale*self.speedFactor];
        [self.speeds addObject:k];
    }
    
}

- (void)startAnimation{
    self.timer = [NSTimer scheduledTimerWithTimeInterval:0.05 target:self selector:@selector(updateValues) userInfo:nil repeats:YES];
    [[NSRunLoop currentRunLoop] addTimer:self.timer forMode:NSRunLoopCommonModes];
}

- (void)stopAnimation{
    [self.timer invalidate];
    self.timer = nil;
}

- (void)updateValues{
    
    for (int i = 0; i < self.nrBars; i++){
        NSNumber *curVal = (NSNumber *) [self.currentValues objectAtIndex:i];
        NSNumber *curDir = (NSNumber *)[self.currentDirections objectAtIndex:i];
        NSNumber *speed = (NSNumber *)[self.speeds objectAtIndex:i];
        
        float delta = curDir.intValue*speed.floatValue;
        float newVal = curVal.floatValue + delta;
        float rest = 0;
        
        if (newVal > self.maxValue){
            [self.currentDirections replaceObjectAtIndex:i withObject:[[NSNumber alloc] initWithInteger: curDir.intValue * -1] ];
            rest = newVal - self.maxValue;
            newVal = self.maxValue - rest;
        }
        if (newVal < self.minValue){
            [self.currentDirections replaceObjectAtIndex:i withObject:[[NSNumber alloc] initWithInteger: curDir.intValue * -1] ];
            rest = newVal - self.minValue;
            newVal = self.minValue - rest;
        }
        
        [self.currentValues replaceObjectAtIndex:i withObject:[[NSNumber alloc] initWithFloat:newVal]];
    }

    [self setNeedsDisplay];
}

- (void)drawRect:(CGRect)rect {
    
    CGContextRef context = UIGraphicsGetCurrentContext();
    
    //invert y axis, so origin is in left bottom corner
    CGContextScaleCTM(context, 1, -1);
    
    //Set origin to middle of frame
    CGContextTranslateCTM(context, 0, -rect.size.height);
    CGContextTranslateCTM(context, 0, rect.size.height/2);
    
    //Set bar color
    CGFloat r=1, g=0, b=0, a=1;
    [self.barColor getRed:&r green:&g blue:&b alpha:&a];
    [self.barColor setFill];
    
    //Draw bars
    if(self.mirrorBars){
        [self drawBars:context inRect:rect withRelMin:0 andRelMax:1];
        
        [self.barColor getRed:&r green:&g blue:&b alpha:&a];
        [self.barColor setFill];
        CGContextSetRGBFillColor(context, r, g, b, 0.3*a);
        
        CGContextScaleCTM(context, 1, -1);
        [self drawBars:context inRect:rect withRelMin:0 andRelMax:1];
        CGContextScaleCTM(context, 1, -1);
    }
    else{
        [self drawBars:context inRect:rect withRelMin:-1 andRelMax:1];
    }
}

- (void)drawBars:(CGContextRef)context inRect:(CGRect)rect withRelMin:(CGFloat)relMin andRelMax:(CGFloat)relMax{
    
    //Set line width (bar width)
    CGFloat lineWidth = 2;
    
    //Draw bars
    CGFloat absHeight = rect.size.height;
    CGFloat absWidth = rect.size.width;
    CGFloat absMax = absHeight/2;

    for (int i = 0; i < self.nrBars; i++){
        
        float relX = ((float)i)/self.nrBars;
        float relY = ((NSNumber *) [self.currentValues objectAtIndex:i]).floatValue;
        
        int nrBoxes = (int) ceil(abs(relY*relMax*absMax)/lineWidth);
        for(int j = 0; j < nrBoxes; j++){
            UIBezierPath *path = [UIBezierPath bezierPathWithRoundedRect:CGRectMake(0.5 + relX*absWidth, 0.5 + relMin*absMax + j*(lineWidth + 0.5), lineWidth, lineWidth) cornerRadius:2];
            [path fill];
        }
    }

    CGContextDrawPath(context, kCGPathStroke);
}

@end

```
