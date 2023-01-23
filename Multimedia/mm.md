Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
All Chromebooks provide some 3D hardware acceleration in Chrome OS. However, experience with crouton chroot varies depending on the release chosen and the type of hardware.

### Haswell/Bay Trail-based Chromebooks
(e.g. Acer C720 `peppy`, ASUS Chromebook C200 `squawks`)

Haswell/Bay Trail-based Chromebooks require updated drivers to support 3D acceleration.

Working releases:
* Ubuntu 12.04 (`precise`): crouton automatically install backported drivers ([#890](https://github.com/dnschneid/crouton/pull/890)).
* Ubuntu >=13.10 onwards (`saucy`/`trusty`).
* Debian `jessie`/`sid`

Releases that do not work:
* Debian `wheezy`: There are no backported drivers provided by Debian, so this will probably never be supported.
* `kali`: Based on `wheezy`, so no easy fix is available.

### Intel-based Chromebooks on kernel 3.4
(e.g. Acer C710 `parrot`; Cr-48 `x86-mario` is an exception, it works accross the board, as it uses a much older chip: `i915` driver)

OpenGL mesa libraries version >=9 require kernel >=3.6 to work on i965-based hardware (e.g. Sandy/Ivy Bridge). crouton includes a fix to use old mesa libraries on recent Ubuntu releases.

Working releases:
* Ubuntu 12.04 (`precise`)
* Ubuntu >=13.10 onwards (`saucy`/`trusty`): crouton automatically downgrades mesa libraries ([#913](https://github.com/dnschneid/crouton/pull/913)).
* Debian `wheezy`, and `kali`

Releases that do not work:
* Debian `jessie`/`sid`. See [this comment in issue 704](https://github.com/dnschneid/crouton/issues/704#issuecomment-39042864) for a workaround.

### Chromebook Pixel

Should work fine on all releases (it does not use kernel 3.4, nor is Haswell-based).

### ARM-based Chromebooks

Technically possible, but would require a lot of work and there are currently no guides on how to do it.

Most people just dual boot and use a mainline kernel with Mesa drivers (such as Panfrost) instead (vendor drivers only support GLES).

### Tools to check hardware acceleration support

Figure out the PCI id of the video device (see [PCI id database](http://pci-ids.ucw.cz/read/PC/)):
```
cat /sys/class/graphics/fb0/device/vendor
cat /sys/class/graphics/fb0/device/device
```

Check if hardware acceleration works:

 - `glxinfo`: `OpenGL vendor string` should be `Intel` on Intel hardware (not `VMWare`/`llvmpipe`)
 - `xdriinfo`: No error
 - `glxgears -info`: Gears are displayed and running

[Output on test machines](http://drinkcat.github.io/chroagh/2014-07-09_00-37-27_drinkcat_chroagh_x11test.tmp_35.html).

Note: `trusty` users will find `glxinfo` and `glxgears` in the `mesa-utils` package.For most use cases, audio works just fine with CRAS; `ALSA to CRAS` is supported out of the box by crouton, and `JACK to CRAS` is the most convenient way to run JACK apps.

However, direct access to hardware may be desired for more latency-sensitive audio applications. The following is a guide on setting up ALSA and JACK access to hardware on a crouton chroot.

---

## ALSA to hardware

### Permissions
On your chroot, add yourself to the `hwaudio` group.
```
sudo usermod -a -G hwaudio "$USER"
```
Log out and log back in and restart your Chromebook.

Test:
```
$ groups
slee2 hwaudio video sudo plugdev audio input
```
### Stop CRAS

Make sure you've stopped CRAS, or else it will hold on to your audio device. In a ChromeOS crosh shell (not your chroot) run `sudo initctl stop cras`. 

[addendum 2022] The utility `initctl` is from Upstart which has been deprecated since approx. 2015. If it still works in a ChomeOS crosh shell is unknown at the moment (2022). It's not absolutely necessary to stop cras to use jack.

Remark: commands that use `systemctl` (from _systemd_) instead of `service` do **not** work in a chroot and therefore do not work in Crouton. Most Debian/Ubuntu distro's use _systemd_ nowadays. Ubuntu switched from _upstart_ to _systemd_ in 2015.

### Configure ALSA

On your chroot, run `cat /proc/asound/cards` to see the names of your audio devices.

You should see something like this:
```
 0 [HDMI           ]: HDA-Intel - HDA Intel HDMI
                      HDA Intel HDMI at 0xe1218000 irq 65
 1 [bdwrt5677      ]: bdw-rt5677 - bdw-rt5677
                      bdw-rt5677
```

For the purposes of this tutorial, we will be wanting to talk directly to `bdwrt5677`, the internal audio device on the Pixel 2015. Your setup may be different, so replace `bdwrt5677` with your device in the following steps as appropriate.

Override your chroot's audio settings (which by default routes to CRAS) by creating the file `~/.asoundrc` with the following contents:

```
# ~/.asoundrc
# overrides default alsa settings
# To revert back to CRAS, change "type hw" with "type cras". You do not have to change "card".

pcm.!default { 
    type hw
    card bdwrt5677
}

ctl.!default { 
    type hw
    card bdwrt5677
}
```

#### Test
Test ALSA controls with `alsamixer`. If you see the controls for your desired hardware device and not CRAS, congrats!

To go back to running audio on ChromeOS, change `type hw` to `type cras` in your `~/.asoundrc` and run 'sudo initctl start cras'.


## JACK

### Installation
JACK1 and JACK2 are almost completely compatible and both are actively maintained. That being said, you can only choose one...
#### jackd2
```
sudo apt-get install jackd2
```
I found jack2 to be quite finicky with DBUS issues, so make sure you uncheck `Enable DBUS Interface` in `Qjackctrl -> Setup -> Misc`
#### jackd1
```
sudo apt-get install jackd1
```
You may need to remove jackd2.

A prompt may pop up to enable realtime process priorities. Enter yes.

[addendum 2022] You do not necessarily have to use "jack to cras". Determine the number of your audio device with:
```
cat /proc/asound/card
```
If the number of your audio device is 2 then start jack:
```
jackd -d alsa --device hw:2
```

## JACK TO CRAS (easiest setup)

The beauty of this solution, is that JACK basically gets rerouted to `cras`. 
No need to stop/start cras (which is needed for JACK to hardware, in case you *really* want hyperlow latency).

1. recommended: get pulseauydio out of our way: `apt-get purge pulseaudio; rm /usr/share/alsa/alsa.conf.d/*-pulseaudio*`
2. just run `/usr/bin/jackd -r -s -v -d alsa -P cras &` 
3. test it: run `jack_simple_client` in the terminal and press CTRL-C to stop the audio.

Voila!

> Now put step #2 in a startupscript (`/etc/X11/xinit/xinitrc` or a windowmanager-startupscript), so it starts automatically. If you start `qjackctl` now, it will connect to the jackd-server above.

### Why not start jackd thru qjackctl?

Qjackctl offers many configuration options, however cras does not show up as device.
Qjackctl seems to list only hardware devices (which are currently in use / blocked by cras), and not software interfaces (like 'cras'). There is a workaround: typing 'cras' directly in the device-dropdown, however this is easy to forget & very counter-intuitive. You can still go this route ofcourse, but [you have been warned](https://github.com/dnschneid/crouton/issues/826#issuecomment-43496827).


> NO ERRORS BUT NO SOUND using qjackctl? Increase the Frames/Period-number in the 'Parameters'-tab (512 or bigger), and stop/start qjackctl.
` or a windowmanager-startupscript)

## JACK to hardware

> NOTE: Only do this if you really need lowlatency / hardware-access.

### Set up ALSA
Make sure ALSA is talking directly to the hardware via the earlier steps, as you will be running ALSA as your JACK driver backend.

### Stop CRAS
```
sudo initctl stop cras
```

(this means chrome will not play youtube-videos etc. To start cras once again, run `sudo initctl start cras`)

### Set up realtime process priorities
#### Edit security policy configuration

On Ubuntu, the JACK installation should bring up a prompt if realtime process priorities should be enabled.
If you missed it, you can re-run it with `sudo dpkg-reconfigure -p high jackd`. This creates a a file at `/etc/security/limits.d/audio.conf` with PAM rules granting your user to unlimited memory access and higher CPU priorities.

You may also do this manually, appending the following lines to the bottom of your `/etc/security/limits.conf`:
```
@audio -  rtprio     95
@audio -  memlock    unlimited
```

#### Enable PAM 

[Source](https://wiki.archlinux.org/index.php/Realtime_process_management#Tips_and_tricks)

Crouton uses `su` for login, which by default in Ubuntu has PAM user limits disabled. We need to enable it.

Uncomment the following line in `/etc/pam.d/su`

```
# Sets up user limits, please uncomment and read /etc/security/limits.conf
# to enable this functionality.
# (Replaces the use of /etc/limits in old login)
session    required   pam_limits.so
```

#### Test
Log out and log back in. Run `ulimit -r -l` to see if your user now has a more permissive security policy.

Your output should be as follows:
```
$ ulimit -r -l
-l: locked-in-memory size (kbytes)  unlimited
-r: max rt priority                 95
```

### Fiddle with qjackctrl/jackd

Either with `qjackctl`(gui) or  `jackd` (cli), point your audio device to `hw:bdwrt5677` or whatever your audio device is called. You may have to fiddle with the settings/parameters, as certain combinations of options will not run with your device.  It can also help to prefix these with the `pasuspender` keyword, to temporarily suspend pulseaudio while running the gui or cli.

Example `~/.jackdrc`: (automatically generated via qjackctl)
```
/usr/bin/jackd -dalsa -dhw:bdwrt5677 -r44100 -p1024 -n2
```

Another example `~/.jackdrc`:

```
/usr/bin/jackd -dalsa -dhw:Audio -r48000 -p1024 -n2 -P
```

Note the `-P`, which is the flag for playback only. For this particular USB audio device, JACK threw errors and refused to run duplex mode. Your audio device may behave similarly and require fiddling with.

### Test
Once you have JACK running error free, test it with `mplayer -ao jack song.mp3`. Change your volume settings via `alsamixer`. If it all works, congrats! Now you have realtime audio superpowers on your Chromebook!




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

JamesAudio in crouton should work out of the box, with sound control synchronized between Chrome OS and crouton.
By default, audio is forwarded to Chromium OS Audio Server (CRAS), and you will be able to play audio from both Chromium OS and crouton at the same time, without problem.

If sound doesn't work, [upgrading your chroot](https://github.com/dnschneid/crouton/wiki/Common-issues-and-reporting) is very likely to fix your issue.

## Pulseaudio

Some applications may not be able to talk with the CRAS plugin directly. In that case, installing `pulseaudio` (and `pulseaudio:i386` or `libpulse0:i386` for Skype) may help (most DE/WM start it automatically, but you can also start it manually with `pulseaudio --start`).

Possible workaround for Civ 5, tested on a C720-3404 with Ubuntu Trusty and XFCE4.
Modify /etc/pulse/daemon.conf by uncommenting/changing the following lines:
```
default-sample-rate = 48000
default-fragments = 5
default-fragment-size-msec = 2
```
## Midi

Some applications (dosbox, musescore, a2jmidi, ...) require access to the sequencer (`/dev/snd/seq`). The simplest fix is to modify `/etc/rc.local` in the chroot, and add this statement:
```
chmod a+rw /dev/snd/seq
```
Then, restart your chroot.

## Direct access to the hardware

In some very specific cases (e.g., running advanced audio software where individual outputs is required), you may want to [get direct access to the audio hardware](https://github.com/dnschneid/crouton/wiki/Access-audio-hardware-directly-(ALSA,-JACK)). You can do so by adding your chroot user to the `hwaudio` group.

## Alsa device name

To access the *cras* audio system from `amixer` and `alsamixer`, use the *cras* device with `-D cras` as a parameter.In your home directory, open a terminal and type `mkdir .pulse`. Then, `cd .pulse` and create a single file called client.conf, which looks like this:  

`autospawn = no`

That's all folks! Exit your chroot, crank it back up, and pulseaudio will be "dead".  

Afterwards, you can control pulseaudio with `pulseaudio --start` and `pulseaudio --kill`.

Conversely, if you need pulseaudio to work (for example for Firefox), you may need to create file `$HOME/.config/pulse/client.conf` with the single line:

`autospawn = yes`

This will ensure that the pulseaudio daemon gets started when you need it for any program that depends on it!
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

(Page adapted from [a post](http://linuxmusicians.com/viewtopic.php?f=47&t=11394) at the LinuxMusicians.com KXStudio forum)It is not necessary to run Asunder as root: just add your username to the cd-rom group, then restart your chroot. Likewise, add your username to hwaudio to gain control over audio hardware. Thanks, James

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

*So now, I just plug my CD/DVD reader/writer into two USB ports, load the CD I want to rip and select Asunder from the menu. (Having changed the permissions of /dev/sr0 I can of course also watch DVDs.)*Route JACK through CRAS# Using JACK for SimpleScreenRecorder
## Permissions
Go to `/etc/security/limits.conf` and add these lines:
```
@audio          -       rtprio          80
@audio          -       memlock         unlimited
```
Then open a terminal and run:
`sudo usermod -a -G audio $USER && sudo adduser $USER hwaudio`
## Setup
Remove: `sudo apt-get remove pulseaudio`

Install: `sudo apt-get install qjackctl jackd2`

Create a `.asoundrc` in your home directory (`~/.asoundrc`) with the following contents:
```
pcm.!default {
    type plug
    slave { pcm "rawjack" }
}

pcm.rawjack {
    type jack
    playback_ports {
        0 system:playback_1
        1 system:playback_2
    }
    capture_ports {
        0 system:capture_1
        1 system:capture_2
    }
}

pcm.jack {
    type plug
    slave { pcm "rawjack" }
    hint {
    description "JACK Audio Connection Kit"
    }
}
```
Now restart your chroot.
## Making Sound
Do `sudo stop cras`

Open QJackCtl (found under the 'Multimedia' tab in Xfce) and press start. Try recording with SSR. If there are no frames in either in or out, try executing `jackd -r -d alsa -r 44100`

If SSR works and you want JACK to automatically run on chroot startup, create a file called `.jackdrc` in your home directory. Set it to this:
```
sudo stop cras
jackd -r -d alsa -r 44100
```
## I'm done...
Stop JACK by CTRL+C-ing it in the terminal if you have it up, AND stop it in QJackCtl. Then execute `sudo start cras`