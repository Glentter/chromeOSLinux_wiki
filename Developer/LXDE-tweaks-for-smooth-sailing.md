LXDE works best with the trusty release. I will assume you are using -r trusty -t lxde, and will try to offer alternatives when possible. LXDE is lean but powerful, yet some files need to be edited manually. Make the following tweaks, then exit and restart your chroot for the changes to take effect.

1. Double-Click Speed: create the file `.gtkrc-2.0` in your home folder. It should contain the single line:  

`gtk-double-click-time=1000`  

The time above is in milliseconds, so for 1/2 second it would be 500. Without this config file, the default is 200.  

2. To avoid "not allowed" errors when you insert an external CD, DVD, USB, or MicroSD, add a package, as follows:

`sudo apt-get install policykit-1-gnome`  

After the install, from the LXDE menu click on Preferences and then Desktop Session Settings, and place a checkmark in Policykit Authentication Agent, then click OK. Finally, delete the line referring to this package in `.config/lxsession/LXDE/autostart`  

3. Regarding the above autostart file, I like to add 2 lines of my own:  

`@syndaemon -i 2 -K d`  
`@xrandr --output eDP1 --brightness .75`  

NOTE: If you are using Debian Stretch, the 1st line above will not work. Also, eDP1 needs to be replaced with eDP-1. Stretch uses a totally different approach to managing keyboard and mouse input events. I will cover this in another turorial, then come back and link to it here.  

It is worth noting that neither of these autostart lines will have any effect if you are running in xiwi mode.  

4. lxterminal: I like to add lxterminal to the left-hand Application Launch Bar. It is too tedious to describe in detail, just right-click on the panel and select panel settings. You'll figure it out...

***

EDIT: The following no longer seems to be necessary. I just installed a fresh chroot using -r trusty and -t lxde,xorg,xiwi,keyboard and the video worked without a glitch!

5. If you not yet tweaked your xorg settings, refer to this hack:

[https://github.com/dnschneid/crouton/wiki/Fix-intel-xorg-problems-in-crouton](https://github.com/dnschneid/crouton/wiki/Fix-intel-xorg-problems-in-crouton)  

Exit your chroot, fire it up, and test everything out. This was a difficult tutorial to write, so please let me know of you see anything amiss. Thanks!