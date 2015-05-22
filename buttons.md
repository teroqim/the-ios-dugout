Buttons
=======
We hate them, we love them. Here's how to tango with them.

Selected/highlighted state
-----------------------------------------------------------

- To disable alpha changes in the title color when highlighting/selecting a button, change the type of the button to 'Custom'. This can be done in the Storyboard.
- To take actions on selected/highlighted state, eg changing color of the button in selected state, add a target to the button:
   ```objective-c
   [button addTarget:self action:@selector(highlightButton) forControlEvents:UIControlEventTouchDown];
   
   ...
   
   - (void)highlightButton{
      [button setBackgroundColor:[UIColor greenColor]];
   }
   ```
- To change title color for different states, set type to custom, then use these methods:  
   ```objective-c
   [button setTitleColor:[UIColor whiteColor] forState:UIControlStateNormal];
   [button setTitleColor:[UIColor greenColor] forState:UIControlStateHighlighted];
   [button setTitleColor:[UIColor greenColor] forState:UIControlStateSelected];
   
   ```
