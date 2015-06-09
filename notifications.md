Notifications
=============
Sending and receiving notifications through the notification center is essential to any app with asynchronous parts. Here is the boilerplate code I use for messaging.

Sending message:
```objective-c
NSMutableDictionary *dic = [NSMutableDictionary new];
[dic setObject:@"hey" forKey:@"message"];
[[NSNotificationCenter defaultCenter] postNotificationName:DownloadFinishedNotification object:self userInfo:dic];
```

Receiving message:
```objective-c
@property id<NSObject> downloadFinishedObserver;

...

- (void)viewDidLoad { //Or in some init method
    [super viewDidLoad];
    
    NSOperationQueue *mainQueue = [NSOperationQueue mainQueue];
    __weak __block TViewController *obsSelf = self;
    self.downloadFinishedObserver = [[NSNotificationCenter defaultCenter]
                                   addObserverForName:DownloadFinishedNotification
                                   object:nil
                                   queue:mainQueue
                                   usingBlock:^(NSNotification *note){
                                       //Handle event
                                       [obsSelf handleDownloadFinished];
                                   }];
}

- (void)dealloc{
    [[NSNotificationCenter defaultCenter] removeObserver:self.downloadFinishedObserver];
}
```
