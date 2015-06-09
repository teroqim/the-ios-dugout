Miscellaneous
=============
Tips and tricks that haven't found a real home yet.

Act on deletion of table view cell after animation
--------------------------------------------------
If you're removing a row from a table view using deleteRowsAtIndexPaths:withRowAnimation: you might want to do some cleanup or bookeping
after the animation have finished. It is not obvious how to make it happen, but here is one way of doing it:
```objective-c
[CATransaction begin];
__weak __block TTableViewController *blockSelf = self;
[CATransaction setCompletionBlock:^{
    //This will happen after the delete animation has finished
    [blockSelf.tableView reloadData];
}];
    
[self.tableView beginUpdates];
[self.tableView deleteRowsAtIndexPaths:@[indexPath, indexPathSeparator]
                      withRowAnimation:UITableViewRowAnimationFade];
[self.tableView endUpdates];
    
[CATransaction commit];
```
