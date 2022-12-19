## General info
**NOTE:** The originator of this Howto is no longer supporting it. Due to changes in the ChromeOS kernel source handling, it is no longer generally possible to compile the Virtualbox module for ChromeOS. Instead, the originator recommends dual-booting the Chromebook into "native" non-ChromeOS Linux and running VirtualBox under a standard kernel. See [This comment on issue 45](https://github.com/divx118/crouton-packages/issues/45#issuecomment-281434222).

This guide is tested on:  
HP chromebook 14 (Celeron 2955U)  
Acer c720 (i3-4005U)  
Acer R11 CB5-132T-C1LK

However, it should work for any x86 chromebook. Just be sure you clone the right kernel source for your device.

For installing virtualbox we need a kernel headers package to compile the virtualbox modules. However there are no kernel headers available to install that work with the kernel of your chromebook. That is why we need to build our own. In the section below we explain how you can boxodo that.   

**NOTE:** If you are just interested in installing kernel headers and virtualbox you can go and read this [README.md](https://github.com/divx118/crouton-packages/blob/master/README.md) It is a ppa with most of the kernel versions and architectures for chromebooks. **You can post any problems by creating a** [new issue on divx118/crouton-packages git](https://github.com/divx118/crouton-packages/issues/new)


## Getting the kernel source

There are two git repos [kernel](https://chromium.googlesource.com/chromiumos/third_party/kernel/) and [kernel-next](https://chromium.googlesource.com/chromiumos/third_party/kernel-next/) where the kernel sources can be found, but you have to know which branch you're on. Branches can be identified by chromeos-**_version_** where version is your kernel version.  
**NOTE:** Mostly you will need the [kernel](https://chromium.googlesource.com/chromiumos/third_party/kernel/) repo.  
On the HP chromebook 14 I have kernel 3.8.11 so I need the chromeos-3.8 branch in the [kernel repo](https://chromium.googlesource.com/chromiumos/third_party/kernel/).

Determining the appropriate branch may not be as straightforward as chromeos-[kernel version] but you should probably still start there. Take a look at [Issue 3342](https://github.com/dnschneid/crouton/issues/3342) for tips on finding the right branch. Also, [Issue 2642](https://github.com/dnschneid/crouton/issues/2642) contains a post with a [script](https://github.com/dnschneid/crouton/files/366435/git-rewind.zip) to help automate finding the right commit in the branch.

First we need some tools installed in our chroot. I did this on a precise with xfce chroot, but it should also work on others.

**Note:** All the shell commands on this page need to be done in the shell of your chroot if not specified otherwise.

```
$ sudo apt-get install git-core make kernel-package bc
```
Next, we need to determine the version of the kernel we're currently running.

```
$ uname -r
```

Then clone the kernel branch chromeos-[version] to our home folder.

```
$ cd ~
$ git clone https://chromium.googlesource.com/chromiumos/third_party/kernel -b chromeos-[version]
```

For example, [version] may be 3.18 or some other value; see the value returned by uname above to determine this.  Then we may need to download the correct revision of the kernel source from the git repository, if the downloaded source version doesn't match that of what we're currently running, by rolling back to a previous commit.  First, determine the kernel version for the source we just downloaded:

```
$ cd kernel
$ ./chromeos/scripts/prepareconfig chromeos-intel-pineview
$ make oldconfig
$ make kernelrelease
```

If the version from the uname command above does not match what is displayed, and is indeed a later version, then one can do the following to download an earlier version:

```
$ git reset --hard origin/chromeos-3.18
$ git checkout HEAD~[number of commits prior to current version]
$ ./chromeos/scripts/prepareconfig chromeos-intel-pineview
$ make oldconfig
$ make kernelrelease
```

One may need to repeat the above steps a number of times, each time increasing the commit number in brackets, to get back to the appropriate version of the kernel source, and some trial and error may be needed to find your matching version.

Because we're not building the latest version of the source in the git repository, building a package from an older version will result in a package and installed version appended with "-dirty".  This can cause problems when building the Virtualbox modules later.  The simplest way to prevent this is to make a quick edit to script scripts/setlocalversion.  Search for text "-dirty" and remark out the "case" and "if" structures that append the text "-dirty".

Before we start building we need to setup our kernel config. More info can be found [on the chromium dev pages](http://dev.chromium.org/chromium-os/how-tos-and-troubleshooting/kernel-configuration)

First I had to run with -Werror, because I got one small problem when building some wifi module. This could be a problem with the gcc compiler I used.  
Just use your favorite text editor and edit "chromeos/config/base.config" in the kernel dir.

```
$ cd kernel
$ vi chromeos/config/base.config
```

Search for CONFIG_ERROR_ON_WARNING=y and make it CONFIG_ERROR_ON_WARNING=n

Look at which flavor you need for your chromebook. We have x86_64 so we setup the config for chromeos-intel-pineview.  
More info on which flavor to choose see the kernel config info site mentioned above.

Before proceeding, don't forget to modify the script scripts/setlocalversion to remove the conditionals that append the text "-dirty" to the kernel version and package name; see above.

```
$ ./chromeos/scripts/prepareconfig chromeos-intel-pineview
$ make oldconfig
```

> To build the kernel on a Chromebook Pixel 2 with a 3.14 Kernel gcc 4.9 was required.  If you get this error trying to build, this may be the cause:

```
 gcc: error: unrecognized command line option ‘-fstack-protector-strong’
```

For an Ubuntu Trusty chroot, the following will upgrade to gcc 4.9:

```
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
# if command fails with add-apt-repository command not found then sudo apt-get install software-properties-common
sudo apt-get update
sudo apt-get install g++-4.9
# delete link to old gcc version
sudo rm /usr/bin/gcc
# link to new gcc version
sudo ln -s /usr/bin/gcc-4.9 /usr/bin/gcc
```

## Building kernel headers and kernel image

Now we can start building kernel_headers and kernel_image. Note that this will take some time.

```
$ make-kpkg --rootcmd fakeroot kernel_image kernel_headers
```

> This failed with the following error when building 3.14 kernel:

```
cannot create /etc/apt/apt.conf.d//01autoremove-kernels.dpkg-new: Permission denied
```

Which was solved by running the make as __sudo__.

After it is finished without errors you should now see two *.deb files in the directory above kernel. If you followed this guide to the letter it should be your home dir.

```
$ ls -al ~ | grep .deb
-rw-r--r--  1 root    root    8340026 Dec 27 18:08 linux-headers-3.8.11_3.8.11-10.00.Custom_amd64.deb
-rw-r--r--  1 root    root    7656850 Dec 27 18:07 linux-image-3.8.11_3.8.11-10.00.Custom_amd64.deb
```

**DO NOT INSTALL THE .deb PACKAGES NOW!!!** Please follow the steps and unmount your /lib/modules first!
Otherwise, you have the risk of overwriting Chrome OS kernel modules causing Chrome OS won't boot and you'll have to do a SD card or USB recovery. In that case, YOUR DATA WILL BE LOST!

## Installing kernel headers/image

Before we can install we need to umount /lib/modules which is bind mounted in enter-chroot.  
We also need to disable module_locking for loading the vboxdrv kernel modules and actually load the modules.  
Loading the modules can be done in rc.local. For disable module_locking we need to change the kernel flags. See [Repack-kernel-to-Enable-VT_x-for-Virtualbox](https://github.com/dnschneid/crouton/wiki/Repack-kernel-to-Enable-VT_x-for-Virtualbox).  
I also created a script [change-kernel-flags](https://github.com/divx118/crouton-packages/blob/master/change-kernel-flags) to do this that can be found on [divx118/crouton-packages](https://github.com/divx118/crouton-packages/blob/master/README.md).
 

Create the following /etc/rc.local or add this to your /etc/rc.local if you already have one. 

```bash
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.

# umount bindmounts /lib/modules from enter-chroot
for m in `cat /proc/mounts | /usr/bin/cut -d ' ' -f2 | grep /lib/modules| grep -v "^/$" `; do
        umount "$m"
done
# try to load vboxdrv modules
if [ `find /lib/modules/"\`uname -r\`" -name vboxdrv.ko` ]; then
    # check if module_locking is disabled
    if ! grep -q "module_locking=0" /proc/cmdline; then
        exit 2
    fi
    modprobe vboxdrv
    modprobe vboxpci
    modprobe vboxnetadp
    modprobe vboxnetflt
fi

exit 0
```
Also be sure to change the execution bit 
```
$ sudo chmod +x /etc/rc.local
```

You can now logout and login again in your chroot to see if the script rc.local works.  
If all is well, everything in /lib/modules is umounted so we can install our two deb files.  
You can check this with the following.
```
$ cat /proc/mounts | grep /lib/modules
```

Only if you build the kernel headers yourself install them by running dpkg
```
sudo dpkg -i linux-image-3.8.11_3.8.11-10.00.Custom_amd64.deb
sudo dpkg -i linux-headers-3.8.11_3.8.11-10.00.Custom_amd64.deb
```

Now you can [download virtualbox](https://www.virtualbox.org/wiki/Linux_Downloads) and install it.
After installing, logout of your chroot and log back in. Virtualbox drivers should now be loaded. You can check it by looking at lsmod or cat /proc/modules.
**Note:** If you don't download virtualbox from the site, but install it from ubuntu repo with apt, then you will be missing the /etc/init.d/vboxdrv script.  
**Note:** The vboxdrv will stay loaded when you log out of your chroot.  
You can unload it before logging out with

```
$ sudo /etc/init.d/vboxdrv stop
```
Or in a chronos shell

```
$ sudo /sbin/rmmod vboxpci
$ sudo /sbin/rmmod vboxnetadp
$ sudo /sbin/rmmod vboxnetflt
$ sudo /sbin/rmmod vboxdrv
```



