Audio in crouton should work out of the box, with sound control synchronized between Chrome OS and crouton.
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

To access the *cras* audio system from `amixer` and `alsamixer`, use the *cras* device with `-D cras` as a parameter.