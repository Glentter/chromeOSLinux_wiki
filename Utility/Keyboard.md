## Table of Contents

* [Sysrq](#sysrq)
* [Chromebook keyboard model](#chromebook-keyboard-model)
  * [non-US layout](#non-us-layout)
  * [<code>Super_L</code> key](#super_l-key)
    * [Swap Left Control and Search key](#swap-left-control-and-search-key)
  * [Unity/Gnome resets xmodmap customization and keyboard layout](#unitygnome-resets-xmodmap-customization-and-keyboard-layout)
  * [Remove the keyboard model](#remove-the-keyboard-model)
  * [Outstanding issues](#outstanding-issues)
* [Extra key bindings](#extra-key-bindings)
  * [Map Alt+Click to middle button](#map-altclick-to-middle-button)
  * [Put middle button in bottom left corner of pad](#put-middle-button-in-bottom-left-corner-of-pad)
  * [A python script for adding brightness hotkeys](#a-python-script-for-adding-brightness-hotkeys)
  * [<code>volume</code> scripts](#volume-scripts)
  * [Make Modifier Keys act as alternative keys when pressed on their own.](#make-modifier-keys-act-as-alternative-keys-when-pressed-on-their-own)

## Sysrq

Chrome/Chromium OS enables sysrq functionality on the F10/Volume Up key when the system is put in developer mode.  This behavior is reflected in crouton chroot environments and the Alt+F10/Volume Up key combination will be intercepted and interpreted as Alt+sysrq.

## Chromebook keyboard model

Installing crouton's keyboard target provides an xkb overlay that provides similar functions to bindings found in Chromium OS:

`sudo crouton -t keyboard -u`

In order to make the bindings active you will need to edit `/etc/default/keyboard`.  Change the line that starts with `XKBMODEL` to `XKBMODEL="chromebook"`. Your version of OS may require the keyboard model to be set in a different manner.

Chromium OS key bindings can be inspected by typing Ctrl+Alt+/ (or whatever key is directly next to the right Shift key):
 * All Search+key bindings are supported, with the difference that the top row (function keys) maps to F1->F10 by default. Search+function keys map to actual functions (navigation, brightness, volume, etc.).
 * The other bindings (Ctrl+key, Alt+key, ...) are not supported, and will probably never be, as they may interfere with useful key combinations in some Linux applications.

### non-US layout

Non-US layout can be setup by typing: `setxkbmap -layout de` (replace `de` by your layout). Some layouts also need to specify a variant, e.g. `setxkbmap -layout ch -variant fr`. This command needs to be run every time the chroot X server is started (you may want to put it in your window manager startup script or in an `~/.xiwirc` file).

Non-US layouts are compatible with the Chromebook keyboard model, so you still get Search+key bindings.

NOTE: If you have problems with certain characters, especially ones with umlauts (in Finnish/Swedish keyboards), it might be that you don't have correct locale installed. Usually if you have installed only core or x11 target. To fix this, install UTF-8 capable locale, like: en_US.UTF-8 UTF-8. In Debian this is easy:
  * install locales package: `sudo apt-get install locales`
  * run: `sudo dpkg-reconfigure locales` 

### `Super_L` key

Without the `keyboard` target, the Search key maps to `Super_L`, after installing the target, it becomes the overlay key (`Overlay1_Enable`), that allows you to use Search+key combos.

Some window managers require a `Super_L` key, so you can work around this issue by remapping either `Super_L` or `Overlay1_Enable` to another key.

For example, to use Left Alt as overlay key, and Search key as `Super_L`:
```
xmodmap -e "keycode 133 = Super_L"
xmodmap -e "keycode 64 = Overlay1_Enable"
```
or, to keep the Search key as overlay, but use Left Alt as `Super_L` (the first line is not needed if you have not modified the keyboard map before, as keycode 133 maps to `Overlay1_Enable` by default):
```
xmodmap -e "keycode 133 = Overlay1_Enable" # Not needed on "fresh" keymap
xmodmap -e "keycode 64 = Super_L"
```
You can figure out keycodes for all keys by looking at the output of `xev`. But DO NOT remap keycode 134 (normally mapped to `Super_R`) to `Overlay1_Enable`: it won't work (see issue [#362](https://github.com/dnschneid/crouton/issues/362)).

`xmodmap` modifications are lost when you restart the X server, so you should put these in a script that is sourced at session startup. The easiest is to put these commands in a custom shell script, and add the script to the auto-start list of your DE (e.g., instructions for [XFCE](https://wiki.archlinux.org/index.php/xfce#Custom_Startup_Applications))

Alternatively see [this post](https://github.com/AurelienLourot/crouton-articles/blob/master/crouton-caps-lock.md) to simply make the Search key behave as a normal Caps Lock key.

#### Swap Left Control and Search key

Swapping left control with the Search key requires an extra step, as the modifier table also needs to be modified:
```bash
if type croutonversion &>/dev/null; then
  #If this is a crouton chroot, then swap left ctrl and search keys
  xmodmap -e "keycode 133 = Control_L"
  xmodmap -e "keycode 37 = Overlay1_Enable"
  xmodmap -e "add control = Control_L"
  xmodmap -e "remove control = Overlay1_Enable"
fi
````

This may apply to other keys as well, have a look at the output of `xmodmap` before and after swapping the keys, to see if you need to apply a similar trick in your case.

> If you are using XFCE, write a script to swap the left control key and search key using the snippet above 
> and then add this script to XFCE's Application Autostart. (Look under *Settings Manager* > *Session and 
> Startup* > *Application Autostart*, and then click *Add*. For more information, see 
> https://wiki.archlinux.org/index.php/xfce#Custom_Startup_Applications)

### Unity/Gnome resets xmodmap customization and keyboard layout

For some reason, Unity (and possibly Gnome) resets the keyboard layout everytime the user switches to Chromium OS and back ([#953](https://github.com/dnschneid/crouton/issues/953)).

As a workaround, you can disable keyboard layout management in Gnome with:
```
gsettings set org.gnome.settings-daemon.plugins.keyboard active false
```

However, this will break the layout switching widget.

### Remove the keyboard model

If you install the keyboard target, but then realise a standard keyboard layout is preferable for your use case, you can remove the target by removing its entry from `/etc/crouton/targets`, then running:

```
sudo apt-mark unhold xkb-data
sudo apt-get -y install --reinstall xkb-data
```

### Outstanding issues

The keyboard layout works well in most situations, but there are a few outstanding issues, that do not have a simple solution:

 - There is no good way to bind shortcuts to "Search+letter" combos. (There is an ugly hack in [#267](https://github.com/dnschneid/crouton/issues/267), but we do not want to ship that by default)
 - We are able to swap Search key with another key (see this page), but NOT Super_R, for some unknown reason. ([#362](https://github.com/dnschneid/crouton/issues/362))
 - Currently, Search+Alt toggles Caps lock. This is the reverse of Chromium OS, which uses Alt+Search. (this is easy to fix, actually)
 - It would be nice to make it possible to configure if F1/F12 or function keys are the default ([#665](https://github.com/dnschneid/crouton/issues/665))
 - Search key alone does not do anything ([#665](https://github.com/dnschneid/crouton/issues/665))
 - Unity (and possibly Gnome) resets the keyboard layout everytime the user switches to Chromium OS and back ([#953](https://github.com/dnschneid/crouton/issues/953)).

## Extra key bindings

xbindkeys is loaded in the chroot X server, to allow commands to be executed when certain key combination is pressed (e.g. Ctrl+Alt+Back/Forward to switch between Chromium OS and the chroot display). `.xbindkeysrc.scm` in the user home directory is also loaded, if it exists, allowing to add custom bindings.

The configuration file is based on Scheme/Guile, and an example configuration file can be generated with `xbindkeys -dg`.

After modifying `~/.xbindkeysrc.scm`, you need to restart the chroot X server (`pkill -SIGHUP xbindkeys` may not always work).

You can debug syntax errors by running:

`METHOD='x11' xbindkeys -fg /etc/crouton/xbindkeysrc.scm -v`,

replacing `x11` by `xephyr` if you are using Xephyr (ARM or explicit choice)
 
### Map Alt+Click to middle button

Getting a middle click is tricky, especially on the Samsung ARM Chromebook: it requires quite a bit of skill to get 3 fingers down on the trackpad at the same time, and sometimes a right click is generated instead.

If your window manager or applications do not require Alt+Click for any other purpose, you can map it to generate a middle button click:

```
; Add to ~/.xbindkeysrc.scm
; Map Alt+click to middle button
; Map on Release so that it does not appear both buttons are pressed
(xbindkey '(release alt "b:1") "xdotool click --clearmodifiers 2")
```

Make sure `xdotool` is installed in your chroot (e.g. by running `sudo apt-get install xdotool`).

See [this post](https://github.com/AurelienLourot/crouton-articles/blob/master/crouton-emacs-middle-click.md) to make it work in emacs.

### Put middle button in bottom left corner of pad

"synclient" shows the current trackpad settings. At the bottom will be something like:

```
    RightButtonAreaLeft     = 1603
    RightButtonAreaRight    = 0
    RightButtonAreaTop      = 1508
    RightButtonAreaBottom   = 0
    MiddleButtonAreaLeft    = 0
    MiddleButtonAreaRight   = 0
    MiddleButtonAreaTop     = 0
    MiddleButtonAreaBottom  = 0
```
That is set, at least for xfce4, by /usr/share/X11/xorg.conf.d/50-synaptics.conf, by a line in Section "InputClass," which presents those same values in order:
```
Option "SoftButtonAreas" "50% 0 82% 0 0 0 0 0"
```
In this case it's setting the right half of the bottom strip of the pad to be the right button. To add the middle button to the far left front corner, this can work:
```
Option "SoftButtonAreas" "50% 0 82% 0 0 200 1508 0"
```
Percentages or numbers can be used. The full measure of the pad can be got from the top of the synclient output. In the example here, the "1508" is taken from synclient's report of RightButtonAreaTop, the "200" is an arbitrarily small area of the left front corner. Restart the chroot and this will be in effect. To make it fully permanent across system upgrades see the recommendation at the top of the 50-synaptics.conf file on copying to /etc/X11/xorg.conf.d/.

### A python script for adding brightness hotkeys

https://gist.github.com/ubershmekel/6626111

### `volume` scripts

* ALSA - https://gist.github.com/ubershmekel/6626065
* PulseAudio - https://gist.github.com/jsvennevid/8540344

### Make Modifier Keys act as alternative keys when pressed on their own.

[xcape](https://github.com/alols/xcape) is a way to remap modifier keys so that they can be used as separate keys on their own, meaning you can press Search on it's own and get Escape (useful in Vim).
You might remap the Alt keys to work as PageUp and PageDown on their own, for example. 