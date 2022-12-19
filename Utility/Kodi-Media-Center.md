from [kodi.tv](kodi.tv)
>Kodi (formerly XBMC) is a free and open source media player application developed by the XBMC/Kodi Foundation, a non-profit technology consortium. Kodi is available for multiple operating-systems and hardware platforms, featuring a 10-foot user interface for use with televisions and remote controls. It allows users to play and view most videos, music, podcasts, and other digital media files from local and network storage media and the internet.

Kodi is a target that can be installed with `-t kodi`.

### Features/Notes:
- Works best with modern Ubuntu (~16.04)
- can play h.264 & h.265 out of the box (tested with Acer C720 peppy & Toshiba Chromebook 2 swanky)
- smb/samba works out of the box. cannot scan, must enter port with hostname (445). **([Patch #3503](https://github.com/dnschneid/crouton/pull/3503) in review to fix!)**
- volume is separate from ChromeOS. (make sure it's turned up in chromeos)
- DOES NOT work with kiwi/extension
- ChromeOS power management is disabled while kodi is running, this will prevent it from dimming the monitor or suspending while using kodi.  However, this also means if kodi is running in the background, power management will be disabled as well.
- Samsung XE303C12 Chromebook needs t have kodi installed as below, then xserver has to be installed from chroot, then
  xorg and xiwi need to be installed as an update to kodi, then kodi shold be runnable.
 
## My personal recommendation
I recommend making separate from other targets. `sudo sh ~/Downloads/crouton -t kodi -n kodi`

## Tested With: (feel free to add your model)
- Acer C720 (peppy)
- Samsung XE303C12 Chromebook (Daisy) Does not work at all, see below for instructions to make Kodi work with
                                      Samsung XE303c12 Chromebook).
- Toshiba Chromebook 2 (swanky)

## Instructions for setting up on Samsung Chromebook XEC303C12.

- Use `sudo install -Dt /usr/local/bin -m 755 ~/Downloads/crouton`
- Use `sudo crouton -t kodi -n kodi` like before
- Use `sudo enter-chroot`
- Use `sudo apt-get install xserver-xorg-core xserver-xorg-video-dummy xdiagnose`
- then use `exit`
- use `sudo crouton -t kodi,xorg,xiwi -n kodi u` (this will update kodi).
- use `sudo startkodi` (should work, not yet tested).

## TODO
- Fluff out info regarding Kodi uses
- Detail recommended settings