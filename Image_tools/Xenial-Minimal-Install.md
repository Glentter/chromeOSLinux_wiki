Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
A. Install Crouton
`$ sudo install -Dt /usr/local/bin -m 755 ~/Downloads/crouton`

B. Create the ability to have a GUI
`sudo crouton -r xenial -t x11,keyboard,extension`

(The above will take a little while)

NOTE: xorg acceleration will not work in this setup method, so it's not included as a target above.

C. When the above completes, enter your chroot

`sudo enter-chroot`

Test the install with this command

`xinit`

A small xterm window will open against a black background. Press ctrl-d to exit

D. Install openbox

`sudo apt-get install openbox menu nano`

(The above will take a little while)

E. We need to create a few configuration files

`nano .Xresources`

    xterm*font:10x20
    xterm*geometry:80x25
    xterm*background:black
    xterm*foreground:green

`nano .xinitrc`

    xsetroot -solid teal &
    xrdb .Xresources &
    exec openbox

Test your new GUI with the same command we used before

`xinit`

You will see a teal-colored background and a cursor. Right-click using the touchpad, then click on the top item, Terminal Emulator. A very nice xterm will open up with easily readable green letters against a black background.

Congratulations! You can now install anything else you wish from the terminal. What follows is merely a suggestion.


***

Exit your "desktop" and chroot.

Enter your chroot. Now we will use some "spare parts" from LXDE to create a more usable environment

`sudo apt-get install pcmanfm lxpanel lxterminal lxappearance leafpad`

After the above, we need to tweak the .xinitrc file so it appears something like this

`nano .xinitrc`

    xrdb .Xresources &
    croutonpowerd -i &
    syndaemon -i 2 -K -d &
    xscreensaver -no-splash &
    pcmanfm --desktop &
    lxpanel &
    exec openbox

After all the above, from within the chroot, execute

`xinit`

You will now see the LXDE menu at the bottom of the page. Click on its start icon (it looks like a grey or light-blue terminal), choose Preferences, and Customize Look and Feel. Under the widgets tab, change to Clearlooks, and under Icons, choose the Humanity theme, and click OK. Click on the start icon again, choose Preferences, Desktop Preferences, and choose a background color or image. Under Advanced, click "Show menus provided by window managers" and apply the changes, click OK.

Now is a great time to fix the double-click interval with the following. Note that you'll have to exit the "desktop" and re-enter for this change to take effect.

`nano .gtkrc-2.0.mine` and add the code `gtk-double-click-time=1000`

At this point, you have a more usable system! You can tweak other settings similar as you would in LXDE, and add other packages, without encountering the errors associated with installing the LXDE "target" from crouton.

To kickstart your session from ChromeOS, from the shell, type the following command

`sudo enter-chroot -b xinit`

This assumes you only this single chroot.

Limitations: No xorg acceleration. For example, in SMplayer, use x11 instead xv for video. Also, cannot copy & paste between applications. However, the croutonurlhandler will work. You may have to manually press Shift-Ctrl-Alt-LeftArrow the 1st time. No icons on the desktop, not a big deal. Trash can not visible, it's location is `.local/Share/Trash`, and you can empty trash with `rm -R .local/Share/Trash/*'.