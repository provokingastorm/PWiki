

## Using wait()
https://www.trueachievements.com/forum/viewthread.aspx?tid=768329&anchor=6133653#m6133653
Some tips on waiting:  
  
If you use a literal number, I believe the code doesn't like anything about 4000, so you have a 4 second limit:  
  
**wait(4000);**  
  
However, if you use a variable, you can wait up to ~30 seconds (I believe all variables are 32-bit signed integers, meaning the most-significant-bit is reserved to indicate a negative number, and the largest possible positive number is 32,767). You'll get a warning when you compile, but this works:  
  
**t_delay = 30000;**  
**wait(t_delay);**  
  
If you need longer than 30 seconds, you'll have to get fancy by writing loops, or you can just repeat your delays. For example, to wait 45 seconds:  
  
**t_delay = 22500;**  
**wait(t_delay);**  
**wait(t_delay);**  
  
--  
  
Oh, and that sounds like a fine solution for that RAGE achievement (I watched the video). You might want a very brief delay between A and (I assume) Start, like so:  
  
**# event_press(XB360_A) happened and calls this combo code:**  
**wait(100);**  
**set_val(XB360_START, 100);**  
**wait(60);**  
**wait(40);**  
  
That double-wait at the end actually serves a purpose, too--when you use "set_val" you're telling Cronus to override (force) the specified input to your specified value. That value will be driven to the console until the end of the first wait. At that point, the value of START will revert to whatever the controller is doing. So, assuming you're not actually holding the START button, the code above will "press" it for 60ms and release it for 40.