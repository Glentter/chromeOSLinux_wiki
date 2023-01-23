Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
## Just a simple tutorial to install A Chroot environment to USB or SD card

* Step 1 - Format SD/USB 

Open up ¨Crosh¨ by pressing ctrl+alt+t. (durr)

`crosh> shell`

`chronos@localhost / $ sudo su`

`# fdisk -l`

`...`

`# umount /dev/sdb1` or unmount your removable media in the GUI.

`# mkfs -t ext4 /dev/sdb1`

**NOTE: replace `/dev/sdb1` with the listed name of the external drive. **

The system will automatically rename your sd card to: _**SD Card**_

**NOTE: If you are using a usb drive the system will rename your usb drive to _**USB Drive**_.**

You can also use this resource to help you as well. (This way is harder and more complex.)
[https://github.com/dnschneid/crouton/wiki/Format-external-storage](https://github.com/dnschneid/crouton/wiki/Format-external-storage)

* Step 2 - Super Simple

Unplug and plug back in your removable storage.

* Step 3 - Installation

Download a fresh installer from: https://goo.gl/fd3zc  
`sudo install -Dt /usr/local/bin -m 755 ~/Downloads/crouton`  
`sudo crouton -t ENVIRONMENT, xiwi -p '/media/removable/SD Card'`

Replace `ENVIRONMENT` with the desktop environment that  you wish to install. If you wish to install crouton without the Web Store attachment, just take out `xiwi,` when installing.

**NOTE:** If at any point it outputs something along the lines of: `/media/removable/SD/... is not a valid prefix` Then you need to add a backslash after **_SD_** in **_SD CARD_** This is to tell the computer that there is a space and not just the end of the folder name.   Ex: `/media/removable/SD\ Card/...`


* Step 4 - How To Run External Chroot

`cd /media/removable/SD\ Card/bin`

`sudo sh startunity`

### HELP! It died and I need to clear the carcass.

`sudo rm -r '/media/removable/SD Card/chroots/DISTRO'`

 Replace DISTRO with whatever name of installation (wheezy, jessie, stretch, buster, sid, Kali-Rolling, trusty, and xenial.) NOTE: there are other versions however, they may require extra effort to get them working.


***
* Tip

Some external media will disappear after system suspend, prevent that by disabling power management or installing [Keep Awake](https://chrome.google.com/webstore/detail/keep-awake/bijihlabcfdnabacffofojgmehjdielb)