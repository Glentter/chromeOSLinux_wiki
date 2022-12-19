For the dozens of us (dozens!) who still prefer Window Maker's fast, efficient, and highly stable window manager, it is very easy to quickly install in crouton using x11.

# Using WMaker in crouton 
The below assumes you've installed crouton (your chromebook is in developer mode and you've followed instructions to install crouton):

1. Install Crouton:
`$ sudo install -Dt /usr/local/bin -m 755 ~/Downloads/crouton`
2. Create a chroot with X11: 
`$ sudo crouton -r sid -t x11 ` 
(add the `-e` flag for encryption)
3. Enter the chroot:  
`$ sudo enter-chroot -n sid`
4. Install wmaker:  
`$ sudo apt-get install wmaker`
5. Add `exec wmaker` to ~/.xinitrc:  
`$ echo "exec wmaker" > ~/.xinitrc`
6. Exit the chroot:
`$exit`
7. Launch wmaker directly from the crosh shell:  
`$ sudo enter-chroot xinit`
8. Create an alias for starting in wmaker:  
add `alias startwmaker='sudo enter-chroot -n sid xinit'` to your `/home/chronos/user/.bashrc` (chromeos)

### Helpful configurations

* start xterm automatically by adding it to .xinitrc. Try `echo "xterm &" > ~/.xinitrc && echo "exec wmaker --no-clip --no-dock" >> ~/.xinitrc`
* To get trackpad working install synaptic: `sudo apt-get install xserver-xorg-input-synaptics`
* To edit your synaptic settings, visit https://wiki.archlinux.org/index.php/Touchpad_Synaptics#Disable_touchpad_while_typing
* Dunst can be installed and configured to handle notifications for wmaker. Install `notification-daemon` and `libnotify4`.
* To change configurations easily without installing too many other packages try: `WPrefs`


----

