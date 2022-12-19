If you install the lxde target in xenial, launching with startlxde yields an annoying error: "No session for pid xxxx" where the x's represent a process-ID. Here are 5 work-arounds:

1. Ignore the error. Wait until you see the black screen, then press Esc to view the error, and press Esc again. Sometimes you may see the error first, and you may have to click OK to continue. If you leave LXDE running most of the time, this could work for you. This is the recommended solution. The error is perfectly harmless. After clearing the error, everything works correctly.


***


2. Install the lxde target in trusty instead. Current versions of important programs like handbrake, guvcview,  openshot, and simplescreenrecorder are available from respected PPA's. You'll never face this error in trusty. Note that a mirror must be specified to perform the install, since Trusty has reached end-of-life. A list of ubuntu mirrors can be found at [https://launchpad.net/ubuntu/+archivemirrors](https://launchpad.net/ubuntu/+archivemirrors)


***


3. Go ahead and install lxde normally, but then create an .xinitrc file, which will look something like this:

`xsetroot -solid teal &`

`lxpanel --profile LXDE &`

`exec openbox`

(Author's note: the above was supposed to be a block of code, but indenting each line 4 spaces didn't work, it appeared as 1 line, so I had to put line breaks. You don't need line breaks in your own code.)

Those of you who are familiar with openbox know that you don't need lxpanel to get menus: you can get those by right-clicking the desktop. However, you need to create a menu.xml file in your home directory, under .config/openbox:

    <?xml version="1.0" encoding="UTF-8"?>
    
    <openbox_menu xmlns="http://openbox.org/"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://openbox.org/
                    file:///usr/share/openbox/menu.xsd">

    <menu id="root-menu" label="Openbox 3">
      <item label="Terminal emulator">
        <action name="Execute"><execute>x-terminal-emulator</execute></action>
      </item>
      <item label="Web browser">
        <action name="Execute"><execute>x-www-browser</execute></action>
      </item>
      <!-- This requires the presence of the 'menu' package to work -->
      <menu id="/Debian" />
    
      <menu execute="openbox-menu lxde-applications.menu" id="apps" label="Applications"/>
    
      <separator />
      <menu id="client-list-menu" />
      <separator />
      <item label="ObConf">
        <action name="Execute"><execute>obconf</execute></action>
      </item>
      <item label="Reconfigure">
        <action name="Reconfigure" />
      </item>
      <item label="Restart">
        <action name="Restart" />
      </item>
      <separator />
      <item label="Exit">
        <action name="Exit" />
      </item>
    </menu>
    
    </openbox_menu>

The above only scratches the surface of what can be done using openbox and lxpanel. After I had installed menu, openbox-menu, and games-toys, I created the following .xinitrc:

    croutonpowerd -i &
    syndaemon -i 2 -K -d &
    #pulseaudio --kill &
    xscreensaver -no-splash &
    xrandr --output eDP1 --brightness .75 &
    xrdb .Xresources &
    xsetroot -solid teal &
    xfishtank -d &
    xplanet -background 0x008080 -latitude 33.689 -longitude -86.6833 &
    lxpanel --profile LXDE &
    exec openbox

At any rate, you get both openbox menus as well as lxpanel menus. Now, to launch your desktop, go:

`sudo enter-chroot -b -n xenial xinit`

You get to have your cake and eat it too! 

NOTE#1: To exit openbox, right-click on the desktop and choose "exit".

NOTE#2: This solution works quite well for debian stretch also; actually, for any distro!

NOTE#3: Remember, this is not a true desktop environment. Copy & Paste between apps will not work, neither will the Crouton extension.


***


4. Install these 2 targets when setting up xenial: xfce and lxde (plus whatever else you wish, of course). The 1st time you launch startxfce4, accept the default configuration. Drag the top bar to the bottom, and make the short bottom bar vertical and drag to the right side of the screen, not too far above the main bar.

Next, under settings, configure "Preferred Programs" and choose PCManFM as your file manager, and lxterminal as your default terminal.

Last but not least, execute the following command:

`openbox --replace & exit`

Now exit the desktop, putting a checkmark in the "Save Session" box (if this is your first visit, it will already be checked). Next time you startxfce4, you'll be running openbox with xfce menus. It's really quite nice. I name my chroot xenial-xflx for this hybrid solution.



***


5. Someone on here has published a way to actually fix the LXDE problem, located here:

[https://github.com/dnschneid/crouton/issues/2489](https://github.com/dnschneid/crouton/issues/2489)

Scroll way down the screen to the entry posted by **d2cmath**. It appears somewhat complex, and I haven't tried it yet.

~Thanks ~James