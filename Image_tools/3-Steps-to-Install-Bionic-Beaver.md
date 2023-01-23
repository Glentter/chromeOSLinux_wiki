Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
EDIT: This how-to has been updated to include more information and to be more helpful. Why bother to upgrade? Xenial is getting out of date. It used to have X-accel but seemingly no more, so I have to use x11 in mplayer. But in bionic, I can use xv. You may wish to consider installing Ubuntu's latest LTS, focal. I'm currently using it, it's great so far, and requires no special tricks to install.

***


If you attempt to install bionic beaver, you find yourself in a catch-22 situation. For example, consider the command,

`sudo crouton -r bionic -t xfce`

In this case, the crouton setup script will fail prior to creating the primary user. You cannot enter the chroot manually to fix the error, because the primary user has not been setup. Re-running the script will not work either. All you can do is to delete the chroot and start over. The problem seems to be the fact that the package **xserver-xorg-video-all** never gets installed, perhaps due to some subtle dependency issue in running debootstrap. Since crouton seems to use the same "pattern" for various releases, this would not seem to be a problem with crouton.

The following trick will allow you to install bionic:

1. Install a base system, as follows:

   `sudo crouton -r bionic -t core,audio,cli-extra`

2. Then, after creating the initial user and password, `sudo enter-chroot` and execute the following:

   `sudo apt-get install xterm xinit`

   The preceding command will install a minimal X11 subsystem, including the package xserver-xorg-video-all.

3. Finally, finish your setup with something like,

   `sudo crouton -n bionic -t x11,xorg,extension,keyboard,xfce -u`

***

Note that bionic uses the new libinput instead of the older synaptics, which means you may need to edit 

`/usr/share/X11/xorg.conf.d/40-libinput.conf`

to get the touchpad to use tap-to-click. Edit the file so the **touchpad** section looks like this:

    Section "InputClass"
            Identifier "libinput touchpad catchall"
            MatchIsTouchpad "on"
            MatchDevicePath "/dev/input/event*"
            Driver "libinput"
            Option "Tapping" "On"
            Option "DisableWhileTyping" "On"
    EndSection

Save the file, exit the chroot, and try again.