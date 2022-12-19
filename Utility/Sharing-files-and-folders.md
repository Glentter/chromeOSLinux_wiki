# Sharing files and folders between ChromeOS and your chroot

The `/etc/crouton/shares` file in your chroot defines directories to map into the chroot from the host.<br>
Order is important: be sure to mount directories before you mount subdirectories of those mounts.

*Outside* the chroot, the only directories that can be mapped are
subdirectories of the following locations:

* downloads: ~/Downloads

  Anything in this folder is encrypted by ChromeOS using the logged in user profile, such that only that user may access it. ChromeOS may delete files from here if space gets low.

* shared:    /mnt/stateful_partition/crouton/shared

  Like chroots themselves, the contents of this directory can be accessed by anybody, without logging in.

* encrypted: ~/crouton/shared

  Like `downloads`, this folder is encrypted by ChromeOS. However, ChromeOS will not delete the contents of this directory.

These directories are all managed by ChromeOS, not the chroot. That means they will not be included in chroot backups.

*Inside* the chroot, any absolute or user-relative path (~/x or ~user/x) is OK. **Using a path that already exists in the chroot will result in that path being overmounted and difficult to access.**

Syntax is as follows:

 `HOSTDIR CHROOTDIR [OPTIONS]`

Directory names can have spaces (if quoted), but no quote characters or relative path elements (..'s).<br>
Options can be any fstab-like options to mount. If unspecified, "exec" is assumed.

## Examples:
 Share a home directory encrypted by the current user's unsynced profile

 `encrypted/home ~`

 Share an unencrypted directory between chroots.

 `shared/home ~/Share`

 Absolute directories on the chroot side work too

 `shared/bin /usr/local/bin exec,suid`

 Share a noexec path that has spaces, and a specific user home directory

 `"encrypted/stupid example" "~omg/Why are you even doing this?" noexec`

## Expose the whole chroot to chromeos

> WARNING: dont delete things unless you want to delete them.

Most chrome-apps don't allow access to the home-folder (only USB and Downloads-folder)
The simplest workaround is:

1. buy a tiny usb stick and plug it into your chromebook:
2. create an empty directory on it, called 'crouton'
3. then enter your crouton chroot, and run this command:

```
mount --bind / /media/removable/YOURUSBDISK/crouton
```

Now all chroot-folders are accessible by chromeos using the USB-folder

> *TODO* automation using fstab  