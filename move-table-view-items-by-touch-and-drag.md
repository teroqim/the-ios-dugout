Reorder by long press and drag
==============================
How to reorder items in a table view by long pressing on the item to move and then dragging it to the new position. 
While dragging the item into position, the item follows the finger. 
If dragging the item to an invalid location and releasing, the item goes back to its original position.  
[TODO: add image or video of effect here]

[TODO: Create project from this and try which methods are actually needed, right now I get the sense some are superfluous. /teroqim]

This piece of code was inspired by the following tutorial written by Soheil Azarpour:  
http://www.raywenderlich.com/63089/cookbook-moving-table-view-cells-with-a-long-press-gesture

```objective-c

@implementation MyTableViewController

#pragma mark UITableViewController overrides
- (void)viewDidLoad {
    UILongPressGestureRecognizer *pressRecognizer = [[UILongPressGestureRecognizer alloc]
                                                     initWithTarget:self action:@selector(respondToPressGesture:)];
    [self.tableView addGestureRecognizer:pressRecognizer];
}

#pragma mark Event handlers
- (void)respondToPressGesture:(UILongPressGestureRecognizer *)recognizer{
    //Set up static tracking data
    static UIView *snapshot = nil;
    static NSIndexPath *sourceIndexPath = nil;
    
    //Get current indexpath
    CGPoint currentLocation = [recognizer locationInView:self.tableView];
    NSIndexPath *indexPath = [self.tableView indexPathForRowAtPoint:currentLocation];
    
    if (recognizer.state == UIGestureRecognizerStateBegan){
        /*
         Beginning a new long press. The main idea here is to take a snapshot of the
         item being pressed (if any), place the vertical center of the snapshot 
         at the touch location and hide the 'real' table view cell.
         */
        if (![self tableView:self.tableView canMoveRowAtIndexPath:indexPath]){
            //Safeguard for if there are any cells that should not be moved.
            return;
        }
        if (indexPath){
            sourceIndexPath = indexPath;
            UITableViewCell *cell = [self.tableView cellForRowAtIndexPath:sourceIndexPath];
            
            /*
             Take a snapshot of the cell at the touch location before updating
             since the view should already be on screen.
             */
            snapshot = [cell snapshotViewAfterScreenUpdates:NO];
            
            //TODO: Check again why these settings are necessary
            snapshot.layer.masksToBounds = NO;
            snapshot.layer.cornerRadius = 0.0;
            
            /*
             Add snapshot as subview to table view, place it on top of the
             source cell and animate the popup of the snapshot, then hide 
             source cell.
             */
            __block CGPoint snapshotCenter = cell.center;
            snapshot.center = snapshotCenter;
            snapshot.alpha = 0;
            [self.tableView addSubview:snapshot];
            [UIView animateWithDuration:0.25 animations:^{
                //Move vertical center of the source cell to the touch location
                snapshotCenter.y = currentLocation.y;
                snapshot.center = snapshotCenter;
                
                //Fade in snapshot
                snapshot.alpha = 0.7;
                
                //Fade out source cell
                cell.alpha = 0.0;
                
            } completion:^(BOOL finished){
                //Finally hide the source cell
                cell.hidden = YES;
            }];
        }
        else{
            /*
             NOTE: This is a long press outside the table view, we should not
             do anything here, hence, this else clause may be removed.
             */
        }
    }
    else if (recognizer.state == UIGestureRecognizerStateChanged){
        /*
         Here the drag move has been started or is continued. The snapshot 
         should follow the finger as it moves around.
         If the finger moves in over another view cell, that cell should move
         to the position of were the snapshot came from (could be the position 
         of the source view cell or a cell that has been moved before and thus
         has become the new source view cell).
         */
        
        //Place vertical center of snaphot at current touch location
        CGPoint p = snapshot.center;
        p.y = currentLocation.y;
        snapshot.center = p;
        
        //If the snapshot is in the place of another 'movable' cell, move items around
        if (!([self tableView:self.tableView canMoveRowAtIndexPath:indexPath] && [self tableView:self.tableView canMoveRowAtIndexPath:sourceIndexPath])){
            return;
        }
        if (indexPath && ![indexPath isEqual:sourceIndexPath]){
            [self tableView:self.tableView moveRowAtIndexPath:sourceIndexPath toIndexPath:indexPath];
            [self.tableView moveRowAtIndexPath:sourceIndexPath toIndexPath:indexPath];
            
            //After move, the cell at the touch is the new source
            sourceIndexPath = indexPath;
        }
    }
    else{
        /*
         Drag has ended. Time to remove the snapshot, show the 'real' cell and
         clean up.
         */
        
        //Animate putting back the snapshot into place, hide snapshot and show cell
        UITableViewCell *cell = [self.tableView cellForRowAtIndexPath:sourceIndexPath];
        cell.alpha = 0;
        [UIView animateWithDuration:0.1 animations:^{
            snapshot.center = cell.center;
            snapshot.alpha = 1;
            snapshot.transform = CGAffineTransformIdentity;
            cell.alpha = 1;
        } completion:^(BOOL finished){
            //Cleanup
            [snapshot removeFromSuperview];
            snapshot = nil;
            sourceIndexPath = nil;
            cell.hidden = NO;
        }];
    }
}

#pragma mark UITableViewDataSource
- (BOOL)tableView:(UITableView *)tableView canMoveRowAtIndexPath:(NSIndexPath *)indexPath {
    return YES;
}

- (void)tableView:(UITableView *)tableView moveRowAtIndexPath:(NSIndexPath *)sourceIndexPath toIndexPath:(NSIndexPath *)destinationIndexPath {
    if (sourceIndexPath.row > 0 && destinationIndexPath.row > 0){
        //Make sure to move items around in you datasource here.
        [self.player moveItemFrom:sourceIndexPath.row to:destinationIndexPath.row];
    }
}

@end

```
