Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
The below (which I wrote while still learning about crouton) is way overblown. All in the world you need to do is change your power settings so the Chromebook doesn't sleep when it's inactive & the power's turned on. Be sure this setting's in place before creating or updating a chroot. If you wish the screen to turn off, that's fine. Just don't let it sleep. Always be sure the unit is plugged in before creating or updating a chroot.


***


UPDATE - Since going back to storing my chroot on the built-in SSD, I've experienced almost no stability issues. Worse case is logging out of my chroot, then logging back in too quickly, requiring me to unmount the chroot:

`sudo unmount-chroot xenial -y -f` or whatever your chroot name is.

A possible cause of crashes is switching back and forth between your chroot and ChomeOS, especially when switching to your chroot while a screensaver is running, which can result in a black screen. To get back to ChromeOS, try these steps:

1. Press Shift-Ctrl-Alt-F1 (left arrow)
2. Press Ctrl-Alt-F2 (right arrow)
3. Press Ctrl-Alt-F1 (left arrow)

Now you should be back at ChromeOS.

EDIT - Here is another take on handling your chroots power situation (see below for original post):

Before creating or updating your chroot, go over the VT2 (Ctrl-Alt-F2), login in as chronos, then type

`sudo initctl stop powerd`

and press Enter. This will absolutely prevent your Chromebook from going to sleep. In this case, you can still configure your Chromebook to "sleep when inactive" if you wish to conserve power. Note that after the above command, the brightness controls will no longer work. After your install or update is complete, type

`sudo initctl start powerd`

and press Enter. Finally, in your chroot, make sure you include the following line in your /etc/rc.local:

`croutonpowerd -i &`

All entries in /etc/rc.local must end with the space ampersand in order for crouton's prepare script to work properly. Since your chroot does not actually "boot up", the prepare script permits you to activate servers, mount drives, and do other stuff that would be part of the "init" process of a full install.

NOTE: Many crouton users have reported having to turn off, disable, or uninstall xscreensaver in order to prevent chroot crashes. All I can suggest is, do a burn in test: Setup your chroot, and let it run all night/day with/without the screensaver and see what happens. For safety's sake, mount your chroot in the "normal" place, i.e., your Chromebook's SSD card, prior to moving it to an external location.

Thanks, James

***

My Acer Chromebook 11 C740 runs Crouton quite well. However, if I'm storing the chroot on an SD card, and the Chromebook goes to sleep, if forgets where my SD card is located. Then, I can't eject it from File Manager or the command line, and have to power down and reboot. Then I have to run a command like,

`sudo fsck /dev/sdb`

and wait for it to clean up the file system: if it even can, that is.

***
UPDATE: After losing data for the umpteenth time with a chroot mounted on an SD card, I have gone back to locating my chroots on the Chromebook's SSD. Then I use an SD card or external USB drive to hold my user data.
***

Turn off Screen Lock (under People) and then disable sleep mode (under Device, Power). Do not even allow the screen to turn off if inactive. You can always lock your Chromebook manually if you need to.

Hope this saves you hours of frustration from losing data.

Having said all this, in all honesty, your chroot will likely tend to crash from time to time, especially if you have only 2 GB RAM and a 16 GB SSD, as does my C740, and more especially, if the chroot is stored on an external MicroSD card.

Crouton is the cleverest script I have ever seen for running Linux in a chroot jail. However, if you wish for greater stability, a full Linux install may be the best way to go.

Thanks ~James