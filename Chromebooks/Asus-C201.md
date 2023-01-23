Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
The Asus C201 is a new, inexpensive, ARM-based chromebook that clocks in at one of the most affordable chromebooks manufactured to date.  As of July 2015, the computer costs $169 on Amazon.com, making it an extremely appealing option for those on a very tight budget.

Being an ARM-based machine, it is prone to significantly more hurdles than its Intel-driven counterparts.  This article is meant to be a repository for out-of-the-box distribution/target combination testing, to give potential buyers an idea of which environments will work and which ones will not.

Additionally, this can be used as a jumping off point for improvements to the support coverage on ARM based machines.

The ASUS C201 does not work "out of the box" with versions of Ubuntu less than "trusty". To install run the following:

 - `sudo install -Dt /usr/local/bin -m 755 ~/Downloads/crouton` 
 - `sudo crouton -r trusty -t xfce`

And to start xfce:
`sudo startxfce4`

#x11 Does not find any screens/fbdev not found

If you have an earlier chroot than "trusty" ("precise" is currently built in as the default Ubuntu fetch in Crouton) then you must run the following commands before running 'enter-chroot startxfce4', every time you run it.<br>
`sudo enter-chroot`<br>
`sudo rm /dev/dri/card0`<br>
`sudo mv /dev/dri/card1 /dev/dri/card0`

I now have debian jessie up and running with no tabs open except crosh. When Jessie is running i have about 1.2 gigs left of RAM. Plenty fast too.

(I did not need to do the above. Running ubuntu trusty with xfce, xiwi.)

# Target compatibility
todo

# Notes
- Ubuntu Vivid works fine too. You need it to get more recent versions of Emacs from the repositories. Browsing is a bit slow on Chromium but Emacs flies on this machine of course. Overall I get a 2% to 8% CPU usage. Battery life is ridiculously high. 15 hours or more.