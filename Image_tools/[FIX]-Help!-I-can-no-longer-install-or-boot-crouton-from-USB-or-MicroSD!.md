ChromeOS changed something. So now you get an error about too many symlinks. You must remount your USB or MicroSD after each bootup.

Assume your USB or MicroSD is named MyStuff. Here is the command to use:

`sudo mount -o remount,symfollow /media/removable/MyStuff`

Personally, I keep an alias inside .bashrc to handle this for me:

`alias remount="sudo mount -o remount,symfollow /media/removable/MyStuff/"`

Then, upon each bootup, I go to shell and enter the command, `remount`

I hope this helps.