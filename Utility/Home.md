# Introduction

There are tips for using crouton floating all around the interwebs. This wiki is for collecting them in one space. Anything core to the usage of crouton will probably be moved into the [README](https://github.com/dnschneid/crouton/blob/master/README.md), but device-, environment-, and application- specific usage tips can find a [home](https://github.com/dnschneid/crouton/wiki) here.

## Contributing

Feel free to add your tips and tricks to the wiki.  Do test any commands and scripts you post before submitting them, and try to clean up your instructions.  Don't be surprised or offended when the crouton author(s) and other upstanding community members tweak your tips to make the style consistent, or to work in more general situations.  All tips should be placed in sub-pages, linked in the appropriate category.  It's better to make a separate page for your trick than to have a long page of disparate and unorganized tips, but if you have a bunch of one-liner hints that fit into a coherent group, go ahead and combine them into one page.

# Usage Tips & Tricks

 * [[Backups]]: How to safely back up and restore your chroots.
 * [[Security]]: A bit about `crouton` security and why you should enable encryption.
 * [[Keyboard]]: How to install Chromebook keyboard layout, add custom key bindings, etc.
 * [[Languages]]: How to change your language
 * [[udev|udev: manage inserted devices]]: manage inserted devices
 * Power Management: Keep the system and/or screen from sleeping:
    * Disable the screen saver (usually xscreensaver) in Linux
    * Turn off screenblanking [using the `xset` command](http://www.linuxquestions.org/questions/linux-general-1/how-to-disable-screen-blanking-in-x-175011/).
    * Install ['Keep Awake' plugin](https://chrome.google.com/webstore/detail/keep-awake/bijihlabcfdnabacffofojgmehjdielb) within ChromeOS and activate when needed.
    * Disable idle or lid close suspend with [[power manager overrides|Power manager overrides]]
 * [[Autostart Chroot|Autostart crouton chroot at ChromeOS startup]]: Start a crouton chroot automatically when ChromeOS starts.
 * [[Audio]]: Some notes about audio in crouton, and how it (literally) plays with Chrome OS.
 * [[Sharing-files-and-folders]]: Edit `/etc/crouton/shares` to share things with ChromeOS.
 * [Crucial.sh](https://github.com/qrkourier/crouton-crucial/), a helpful wrapper that automates some of these tips
 * [[How To Install To External Drive]]: How To Install A Chroot to an SD or USB.
 * [[Shutting Down Safely]]: avoiding corruption.
 * [[Access external drive from crouton]]: How to access your external media inside of `crouton`!
## Devices

### [[Chromebook Pixel]]
### [[Acer C710 (all models)]]
### [[Acer C720/C720P]]
### [[ASUS C200]]
#### For a list of all or most Chromebook models, features and comparisons, see:  
- '[Chromebook Models](http://en.wikipedia.org/wiki/Chromebook#Chromebook_models)' from the en.wikipedia.org wiki
- '[The Google Chromebook Comparison Chart](http://prodct.info/chromebooks/#f&)' from product.info

## Distributions

### [[Ubuntu]]

### [[Debian]]

### [[Kali]]

## Desktop Environments

### [[awesome]]
### [[i3]]
### [[Window Maker (GNUstep)|wmaker]]
### [[crouton in a Chromium OS window (xiwi)]]
### [[Sommelier (A more native alternative to xiwi)]]

## Applications
* [[Mail]]
* [[Printing]]
* [[VPNC]]: Connect to VPNs not supported by Chromium OS
* [[Using Cisco AnyConnect VPN with openconnect]]
* [[Running servers|Running servers in crouton]]
* [[Full Blown Audio Video Workstation using KDE chroot and KXStudio]] 
* [[SME Storage Made Easy|SME Storage Made Easy package installation for local access]]: Package installation for local access
* [[Build kernel headers and install Virtualbox (x86)]]
* [[Repack kernel to enable VT-x for Virtualbox|Repacking a kernel]]
* [[Screencasting on a Chromebook]]
* [[Ripping CDs]]
* [[Change chroot user password|Change Chroot User Password]]
* [[Word Salad|Word Salad: Target for local WordPress development]]: A target for WordPress LEMP development
* [[LyX with HiDPI]]