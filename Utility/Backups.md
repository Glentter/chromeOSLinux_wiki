# Backups

Use `sudo edit-chroot -b nameofchroot` (in a crosh terminal shell) to back up your chroot, and `sudo edit-chroot -r nameofchroot` to restore it. if `edit-chroot not found`, use `sudo crouton -f nameofchroot.tar.gz` to restore it.  edit-chroot will warn you if you're clobbering a chroot by restoring, in which case you need to run `sudo edit-chroot -rr nameofchroot` (two r's to confirm that you want to clobber it). 

Be aware that the backup file is automatically placed into your Downloads folder. If you are in guest mode there will not be enough space there. Instead, be sure to log in before you run a backup so you can use your hard drive space.

Once the backup of your chroot has completed, it is placed in your ~/Downloads folder by default. It is important to immediately move not only your chroot backup file, but all files in your ~/Downloads folder that are not backed up to either removable storage such as USB drives, and/or cloud storage. The reason why it is so important to move your chroot, and the contents of the ~/Downloads folder is that in some cases your ~/Downloads folder may not retain all of your files, and may in extreme cases be subject to overwriting, like a system /tmp directory. Also, in the event that you need to perform a Powerwash on the system, the contents of your ~/Downloads directory will not be recoverable.

Keep in mind that in most cases, your ~/Downloads directory is accessible from your chroot's /home/username/Downloads directory, in addition and is also likely shared with Chromium OS's Downloads folder. So it is very likely while using your chroot, or Chromium OS, that you may have some important user content in this directory, and it is NOT backed up during a regular chroot backup. Don't forget to back this special shared directory separately.

After backing up your chroot, save the compressed tar file of the chroot to a safe off-line USB device, network storage, or cloud storage, as well as the contents of your ~/Downloads folder. You may wish to make a USB restore image file as well, so if you need to revert to the exact system, chroot, and ~/Downloads data state of your backup, you will be able to in the future.

In the likely event that you run out of drive space on your Chromebook, you can change the source and destination tarball using -f. Use `sudo edit-chroot -f /path/to/folder -b nameofchroot` and then reconcile and restore your backed up ~/Downloads folder.

If you get an error that the -b option is not available in edit-chroot, it means you need to download the latest version of crouton and use it to update your chroot. The crouton update flag is -u. For example `sudo sh -e crouton -r raring -t xfce -u` could be used to update an existing chroot based on the Raring Release and xfce Target. The update includes a newer version of edit-chroot which support the -b and -r options.

In the event that you want to restore a backup to a stock chromebook (with developer mode enabled) you can use the crouton installer with the -f option specifying the backup's tarball, e.g., `sudo sh -e crouton -r raring -t xfce -f /media/removable/SD\ Card/backup/raring-20130617-1234.tar.gz`

## Large Backups are Split

If your chroot is over 4GB in size, it may be automatically split depending on filesystem, as described in [#3513]( https://github.com/dnschneid/crouton/issues/3513#issuecomment-344150514) and in the `edit-chroot` command docs:

```
-s SPLIT    Force a backup archive to be split into SPLIT-sized chunks.
            SPLIT is specified in megabytes (1048576 bytes), and cannot be
            smaller than 10.
            FAT32 filesystems are split by default to fit within 4GB.
```

You don't have to do anything special to restore these backups, but you need to make sure you have all parts of the file together.