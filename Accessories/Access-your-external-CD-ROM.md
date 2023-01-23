Mon Jan 23 13:38:16 PST 2023
This one is simple. To be able to access your external (USB) CD/DVD device, simply add your user name to the cdrom line in `/etc/group`. Exit your chroot, and start it back up. Voilà!!!

Most linux distros will have already done this for you, but in crouton, you have to do it yourself.