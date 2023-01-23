Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023

**If you have a samsung ARM processor, follow these instructions! kxstudio will not work for you as the repositories are all for x86 and x64 architectures.**

* start with just a basic trusty install with audio target included.
  1. Hit *CTRL-ALT-T* to enter crosh and type in `shell` and hit enter to get to the command line.
  2. type `sudo sh -e ~/Downloads/crouton -t chrome,xfce,audio -r trusty`
  3. after its installed (will take a while) then type `sudo startxfce4`
* in the chmod run `sudo apt-get install qjackctl alsaplayer-jack
* Then run `sudo chmod a+rw /dev/snd/seq` to get midi working. 
* Then change the hardware interface to CRAS.
* Setup -> Change Interface to cras (you have to type it in: it does not appear in the list)
* should look like [this](http://imgur.com/nrzpJce)
* Start qjackctl and you should be good to go.


**We cannot make KXStudio work as-is on an ARM based Chromebook, like a Samsung - you'll have to install your own QJacktl and other audio utilities as KXStudio's PPA's are written for x86/64 machines...

--------------------------------------------------------------------------------------------


I have a fully working installation of KDE KXStudio running on a 3-pound, $199 Chromebook! 

Hit *CTRL-ALT-T* to enter crosh and type in `shell` and hit enter to get to the command line.

(Note: Precise or 12.04 did not seem to work with the Chromebook - too many package dependencies were broken.)

Install a 12.10 XFCE desktop using this command next:

`sudo sh -e ~/Downloads/crouton -t chrome,xfce -n kxstudio -r quantal`

Or you can try out the more heavyweight KDE desktop environment, but XFCE4 will run faster.

`sudo sh -e ~/Downloads/crouton -t chrome,kde -n kxstudio -r quantal`

(You can see whether you can pull off both XFCE and KDE with KXSTUDIO at the same time by typing `sudo sh -e ~/Downloads/crouton -t chrome,xfce,kde -n kxstudio -r quantal` though I've not tested whether this would work.)

It takes about 10-15 minutes on a fast connection.

Enter your new XFCE installation now by typing:

`sudo startxfce4`

Or start up KDE if you opted for that by typing:

`sudo startkde`

Note: If you have other more recently installed chroots of xfce or kde, then add the chroot name like this:

`sudo startxfce4 -n kxstudio`

or

`sudo startkde -n kxstudio`

Now go to the [KXStudio:Ubuntu:Upgrade](http://kxstudio.sourceforge.net/Documentation:Ubuntu:Upgrade) page and run all the commands there in your terminal. You will need to install python-software-properties or software-properties-common to get the add-apt-repository command to work:

For release <= ubuntu 12.04  
`sudo apt-get install python-software-properties`

For release >= ubuntu 12.10  
`sudo apt-get install software-properties-common`

Frankly, I seemed to have better luck with the installations if I installed Synaptic and used it to update at this point, over using sudo apt-get dist-upgrade

`sudo apt-get install synaptic`

Install the kxstudio-desktop-xfce desktop now, or the kxstudio-desktop-kde one if you opted to try the KDE environment. 

The meta-all, meta-graphics and meta-video packages are reporting broken packages and do not install, so you have to install each meta package individually. Gimp, Inkscape and Blender are already there, among others. Try installing other video tools you want individually. (kdenlive is already there.) I haven't attempted video editing on my Chromebook yet, but it will be interesting to see how that holds up!

After everything is installed, log-out and it will bring you back to the Chrome OS.

Now enter your KXStudio installation with:

`sudo startxfce4` or `sudo startkde`

You may want to skip this as it might break the audio - still testing latest crouton: Navigate to the KXStudio Welcome Page in System a little ways down and force the settings to be updated, and go ahead and apply the KXStudio theme.

Now log-out and `sudo startxfce4` or `sudo startkde` again and you'll be in KXStudio!

JACK worked from Cadence after manually starting it (auto-start does not seem to work), but I had to go into the ALSA mixer to turn on my headphones at one point (they muted themselves) - You might want to add the Speakers volume control also, but probably not necessary.


It probably is a good idea to stop cras while using the audio in KXStudio. It may not be fully necessary, but strange things may happen if you try to play sounds in Chrome OS (even inadvertently).

You can always verify if cras is running with something like 

`ps -eaf | grep cras`

Run that last command in the crosh shell before starting up KDE/KXStudio.

(Page adapted from [a post](http://linuxmusicians.com/viewtopic.php?f=47&t=11394) at the LinuxMusicians.com KXStudio forum)