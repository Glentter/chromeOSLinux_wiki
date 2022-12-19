There is an article already about disabling CRAS, which I found to be overkill and unnecessary. All in the world you have to do is add your user user name to the hwadudio: entry in /etc/group, then restart your chroot. Nearly all sound packages allow you to choose input and output channels, such as alsa-utils, audacity, guvcview, smplayer, to name just a few. After adding your user name to hwaudio:, restart your chroot, pull up alsamixer, and tweak all available ports and channels. You will see cras, pulse (if installed and enabled), and sound card ports.

When you are satisfied with everything, save your settings with

`sudo alsactl store`

Then add a single line to /etc/rc.local to restore these settings upon bootup:

`alsactl restore`.

QED

Addendum: If you set a default sound device, CRAS will cease to appear in alsamixer unless you manually specify cras. Setting a default is done by adding a file called .asoundrc in your chroot home directory as follows:

    pcm.!default {
        type hw
        card 1
    }
    
    ctl.!default {
        type hw
        card 1
    }

A sound chip in your Chromebook can have more than 1 "card": for my Acer Chromebook 11 C740, card 0 is the HDMI and card 1 is the analog, which is what I use. Your setup may be different. Also, you can add external sound "cards" or audio interface devices, and these will get numbered as well.

This strange looking code appears to resemble the C programming language, which I never learned. Anyhow, you can install this file, then restart your chroot to see which "card" appears first when you use alsamixer, then change it from there.

If you do wish to disable cras, the correct command is a follows:

`sudo initctl stop cras`

This command can be entered in VT2 after logging in as chronos, or from the chrome shell, or it can be added into your /etc/rc.local

In my experiments, stopping cras has a negligible performance improvement: I cannot tell any difference whatsoever.

To reenable cras, just issue the command

`sudo initctl start cras`

James