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

Sticky table header
-------------------
If you have a table view with a header, e.g. an image, and you want it to shrink while scrolling down and to have it stick to the top when it should normally roll out of view.

NOTE: This code is old and awful, I need to review it. Laterrr
```objective-c
- (void)scrollViewDidScroll:(UIScrollView *)scrollView{
    if (scrollView.contentOffset.y >= 0){
        //Shrink
        CGRect rect = self.tableView.tableHeaderView.frame;
        
        //This makes the header stick to the top when scrolling down a bit
        rect.size.height = MAX(320 - scrollView.contentOffset.y, 70);
        self.tableView.tableHeaderView.frame = rect;
        self.tableView.tableHeaderView.bounds = CGRectMake(0, -scrollView.contentOffset.y, rect.size.width, rect.size.height);
    }
}
```

Expand table header on bounce
-----------------------------
How to have a table view header expand and shrink in sync with the bounce.
```objective-c
- (void)scrollViewDidScroll:(UIScrollView *)scrollView{
    if (scrollView.contentOffset.y < 0){
        self.tableView.tableHeaderView.bounds = CGRectMake(0, -scrollView.contentOffset.y, 320 - scrollView.contentOffset.y, 320 - scrollView.contentOffset.y);
    }
}
```
