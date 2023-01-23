Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
For most use cases, audio works just fine with CRAS; `ALSA to CRAS` is supported out of the box by crouton, and `JACK to CRAS` is the most convenient way to run JACK apps.

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




