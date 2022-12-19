**NOTE:** this is just a workaround for the udev section of the wiki, udev is confusing.... 

( reference to issue [#4330](https://github.com/dnschneid/crouton/issues/4330) pertaining the issue of needing to use `sudo` when attempting to access/run contents on an external usb drive )

This is gonna be very simple, so follow the steps **closely**

1. Make sure your External Media is mounted with exec as one of the flags/targets ( ``sudo mount -o remount,exec /media/removable/Drive_Name`` ), I would suggest putting it into the .bashrc file in crosh ( the micro editor is suggested for this, as vim can be broken in crosh. note micro will need to be sudoed on with the following: ``sudo ./micro``, or move it into your bin, to prevent such an annoyance )
2. login to root on your chroot
3. Open `/etc/rc.local`
4. append the following lines per External Device you wish to make accessible: ``sudo chown -R CHROOT_USERNAME:root /media/removable/Drive_Name``

Edit: You do not need sudo in the rc.local file, it's already running as root

5. At the end of the file, be sure that the line ``exit 0`` is still existing, if it is not, add it now.
6. Save and exit the file
7. restart chroot
8. enjoy



---


If the steps above do not work for you, Ask around, maybe an update has occured, and no one got to updating this page yet