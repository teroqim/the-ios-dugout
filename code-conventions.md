Code conventions
================
These are my own conventions I make up as I go along learning about iOS development. Don't agree? Got Suggestion? Write your own conventions file. Or start an issue.  
/teroqim

Objective-C
-----------

###General .m file structure
```objective-c
/*
Import all headers you need. Order them like this:
- Local header files. Always put the header for the class you're implementing at the top.
- C lib includes.
- Global header files.
Put one empty line between each of these categories.
*/
#import "MyClass.h"
#import "MyOtherClass.h"

<stdlib.h>

<AVFoundation/AVPlayer.h>

/*
Add private properties in an "empty" category for the class you're implementing.
*/

/*
Implementation

Use pragma marks, order by view controller methods, event handlers like hitting buttons, delegate methods, helpers 
*/

/*
A word about comments in general.
*/
```

TBC
