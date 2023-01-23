Mon Jan 23 13:38:16 PST 2023
# Using JACK for SimpleScreenRecorder
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