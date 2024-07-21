9Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
**NOTE:** this is just a workaround for the udev section of the wiki, 
This is gonna be very simple, so follow the steps **closely**

1. Make sure your External Media is mounted with exec as one of the flags/targets ( ``sudo mount -o remount,exec /media/removable/Drive_Name`` ), I would suggest putting it into the .bashrc file in crosh ( the micro editor is suggested for this, as vim can be broken in  annoyance )
2. login to root on your chroot
3. Open `/etc/rc.local`
4. append the following lines e/Drive_Name``

Edit: You do not need sudo in the rc.local file, it's already running as root

5. At the end of the file, be sure that the line ``exit 0`` is still existing, if it is not, add it now.
6. Save and exit the file
7. restart chroot
8. enjoy



---


If the steps above do not work for you, Ask around, maybe an update has occured, and no one got to updating this page yet