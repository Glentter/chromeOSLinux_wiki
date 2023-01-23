Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
## Browsing smb shares in a chroot

**UPDATE:** ChromeOS 73+ supports FUSE, so using 
```
$ gvfs-mount smb://servername/sharename$
```
in an Ubuntu chroot should work without issues (as tested on a chroot running Bionic). You will get prompted interactively for username, domain/workspace (if applicable), and password; use the -a option if you must authenticate anonymously. Your mount should appear in ~/.gvfs/, though you may need gvfs-bin and gvfs-fuse installed for it to appear; also note that even though _gvfs-mount_ is deprecated in favor of _gio mount_, the syntax is interchangeable. Consider this method before compiling your own CIFS kernel modules.

**Note:** if `gio mount smb://servername/sharename` return spurious "volume doesn’t implement mount" error, try 
```
$ dbus-launch bash
```
first, then issue the `gio mount smb://servername/sharename` command from the new bash command line.

**Note:** This should work on a standard chroot with no additional smb packages installed.  
Mounting smb shares in a chroot is easy. It can be done with browsing with for example file manager "pcmanfm". On LXDE this is the default file manager on xfce we have thunar.
Thunar gave me an error: `Failed to open "/ on ".` Still have to do research on what the problem is.
Unity/gnome/kde needs to be tested.  
 
Therefor on xfce we also install pcmanfm with:
```
$ sudo apt-get install pcmanfm
```
Then one problem still exists when you want to browse to your network drives you will have to open some ports of the chromeos firewall. See http://terokarvinen.com/linux/doc/samba-quickstart.html bottom of the page.
So open a crosh shell ctrl-alt-t and type shell.  
**Note:** You need to replace the ip address (10.0.0.1) with the one from your local lan.  
**Note:** On a reboot the iptables are reset to default, so you will need to reapply it when rebooted.

```
crosh> shell
chronos@localhost / $ sudo iptables -I INPUT 1 -p udp --source 10.0.0.1/255.255.255.0 --dport 1025:65535 -j ACCEPT
chronos@localhost / $ 
```

## Browsing smb shares in the File app from chrome

1. Chrome OS has a native SMB file share "mount" utility built into the Files app as of v71+. (The term mount is used loosely here, as no actual mounting appears to be done; at least, Chrome OS seems to show files opened from an SMB share as an encoded SMB share URL of sorts with the externalfile: URI scheme as opposed to file:/// for Google Drive files.)

2. Install [Network File Share for Chrome OS]( https://chrome.google.com/webstore/detail/network-file-share-for-ch/ndjpildffkeodjdaeebdhnncfhopkajk) app from the webstore. Source can be found [https://github.com/GoogleChrome/chromeos_network_file_share](https://github.com/GoogleChrome/chromeos_network_file_share)
**Note:** These mounts will only show in the File app in chromeos and not in your chroot. Slow file transfer.

3. Install [File System for Windows](https://chrome.google.com/webstore/detail/file-system-for-windows/mfhnnfciefdpolbelmfkpmhhmlkehbdf?utm_source=chrome-app-launcher-search) app from the webstore. Source can be found [https://github.com/yoichiro/chromeos-filesystem-cifs](https://github.com/yoichiro/chromeos-filesystem-cifs).  
**Note:** These mounts will only show in the File app in chromeos and not in your chroot. Slow file transfer.

4. Another way to mount smb shares on your chromebook can be found [https://github.com/divx118/cifs/blob/master/README.md](https://github.com/divx118/cifs/blob/master/README.md). This method uses cifs kernel module and doesn't need to have a chroot setup.  
**Note:** These shares will turn up in the chroot. Fast file transfer.

## Mounting network shares with autofs

**Note:** The following is tested on a standard chroot ubuntu precise and trusty.
For autofs to get working we need a module for our kernel. This can be built following the wiki page [Build chrome os kernel and kernel modules](https://github.com/dnschneid/crouton/wiki/Build-chrome-os-kernel-and-kernel-modules).  
**Note:** You will need to disable module_locking for loading modules see [Enable loading of kernel modules](https://github.com/dnschneid/crouton/wiki/Build-chrome-os-kernel-and-kernel-modules#enable-loading-of-kernel-modules).  
We should now have our kernel module autofs4.ko we can copy it anywhere in the chroot. Let's say we make a dir /opt/modules to put our external modules in. In the example we use a chroot named trusty. Adjust the path of autofs4.ko to where it is on your system.
```
$ sudo enter-chroot -n trusty
$ sudo mkdir -p /opt/modules
$ sudo cp autofs4.ko /opt/modules
```
We need to install autofs in our chroot and this example will mount sshfs drives so we also need sshfs package.
```
$ sudo apt-get install autofs sshfs
```
Configure your autofs to mount the sshfs files by following for example http://www.mccambridge.org/blog/2007/05/totally-seamless-sshfs-under-linux-using-fuse-and-autofs/ which I did.  
Now load your autofs module and start the daemon. In this example we will start the daemon in foreground, if everything works you can put the commands in /etc/rc.local to do it for you everytime the chroot starts. Just leave away the extra flags and use only `/usr/sbin/automount` to run it in the background.
```
$ sudo insmod /opt/modules/autofs4.ko
$ sudo /usr/sbin/automount -d -f -v
```
Your mounted drives should now be working, test them by starting another instance of the chroot. If they are not working see the output of `automount` to troubleshoot.

For mounting cifs and nfs we need to build some additional modules.  
Mounting cifs modules:
 * cifs.ko
 * md4.ko

To build enable the following config in `chromeos/config/base.config`      
**Note:** If asked during `make oldconfig` for additional configs, you can leave them at default by pressing enter. 
```
CONFIG_NETWORK_FILESYSTEMS=y
CONFIG_CRYPTO_MD4=m
CONFIG_CIFS=m
```

Mounting nfs modules:
 * sunrpc.ko
 * grace.ko (may be required with newer kernels e.g 3.18)
 * lockd.ko
 * nfs.ko
 * nfsv2.ko
 * nfsv3.ko

**Note:** You should load the modules in the same order as they are listed above, because of dependencies.  
To build enable the following config in `chromeos/config/base.config`
```
CONFIG_NETWORK_FILESYSTEMS=y
CONFIG_NFS_FS=m
```

**Note:** For my nfs share (from a synology NAS) I needed to add the mount option nolock (`-o nolock`) with nfsv3.ko loaded. nfs.ko needs always to be loaded since nfsv2.ko and nfsv3.ko depends on it.  
You also will need to install nfs-common `sudo apt-get install nfs-common`