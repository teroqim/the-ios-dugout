Separators
==========
Anything about anything that could be classified as a separator.

Table view separator cells
--------------------------
I avoid using the native UITextView line separators. Mostly because the designers I work with often want to 
customize separators in a way the native line separators cannot handle.  
Here is one way to do this. Some people call this approach 'hacky' other do not. I belong to the first group. In essence you create
an extra dynamic cell which you customize in code to behave however you want and then you manipulate the cell row indices to your advantage.  

I start by creating a dynamic cell in the interface builder and set auto-layout constraints so that it 
covers the full width of the screen and then set any height (we'll set this in code later).  
In the view controller code I change the following methods:
```objective-c
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section{
    // Double the number of items in the collection you're using
    return [self.items count] * 2;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath{
    if(indexPath.row % 2 != 0){
        //Separator cell
        UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"separatorCell" forIndexPath:indexPath];
        cell.backgroundColor = [UIColor colorWithRed:179.0/255 green:179.0/255 blue:179.0/255 alpha:0.5];
        cell.selectionStyle = UITableViewCellSelectionStyleNone;
        return cell;
    }
    
    //Remember to divide by 2 to get the correct index in your collection
    Item *item = [self.items objectAtIndex:indexPath.row/2];
    
    TTableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"prototypeCell" forIndexPath:indexPath];
    // Configure the cell...
    
    return cell;
}

//Note: Interface builder is a bit limited when it comes to cell heights. For instance, if you want
//to set a height of 0.5 points you need to override this method as shown here.
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath{
    if(indexPath.row % 2 == 0){
        //'Normal' cells
        return 80;
    }
    else{
        //Separator cells
        return 0.5f;
    }
}
```
