Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
It is not necessary to run Asunder as root: just add your username to the cd-rom group, then restart your chroot. Likewise, add your username to hwaudio to gain control over audio hardware. Thanks, James

# Ripping CDs on a Chromebook
## Introduction

Having had a six-year-old Acer Aspire One finally fail and wanting to move on from my eight-year-old desktop, (both running Linux Mint) and having read about `crouton`, I bought a Chromebook and a Chromebox, at a total cost of US$ 320 + tax, with the aim of setting them up the same and doing all my computing on them. While I was sorry not to be able to continue to use Mint, I have found Ubuntu with XFCE easy to set up and use, and I am pleased to be able to report that all software (including Apache with PHP and LaTeX) and hardware (an old OKI laser printer, Wi-Fi, my portable hard drive, and now even the CD/DVD reader/writer) is now working. The only piece of hardware that gave me hard time was the CD reader/writer, which I use to convert my CDs into (legal!!) MP3 files to carry around with me.

## Hardware and Software

* Acer Chromebox CN60
* Samsung Portable DVD Writer SE-208
* Asunder (+ its dependencies)
* lame (for making mp3 files)

(but all of the steps below should also work on other hardware.)

## Steps

My first worry was that the drive was making the right kind of noises when plugged in, BUT there were no /dev/sr0 or /dev/cdrom to be found, nor was _VLC_ able to play the tracks. After a LONG time I realised that even a USB 3 port does not provide enough energy, but using a Y-cable instead to combine the power of two ports, /dev/sr0 and /dev/cdrom were now created.

I knew, from another website, (thanks!) that I had to change the permissions from 660 to 664, and now I was able to play CDs with VLC and view the tracks, with the information from the CDDB database, in Asunder.

*(At this stage already, I think, I would have been able to view DVDs using VLC as well but I didn't try it.)*

BUT nothing happened when I clicked on <kbd>Rip</kbd> in Asunder. After another LONG time I realised that I had to run Asunder as root: when I did, the program created the mp3 files in the folder that I had chosen `/home/user/Music`.

(I had had the same issue with simple-scan: the program starts up, but I have to run it as root for the scanning to happen. So the command in the menu is now:
```
    gksudo simple-scan
```

BUT when I wanted to delete my trial files, I noticed that they had been created by the user root, so I had to change their permissions before being able to move or rename them.

## Putting it all together

So, to make things simple for myself, I created a script called asunder.sh with the following content:

    #!/bin/sh
    chmod -R 664 /dev/sr0
    asunder
    wait
    sleep 1
    chown -R myname:myname /home/myname/Music

and made this file executable. Then in the menu I edited the command to start Asunder to (- but this could also be a launcher on the desktop):

    gksudo /path/to/asunder.sh

*So now, I just plug my CD/DVD reader/writer into two USB ports, load the CD I want to rip and select Asunder from the menu. (Having changed the permissions of /dev/sr0 I can of course also watch DVDs.)*