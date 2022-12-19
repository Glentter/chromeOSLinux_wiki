In your home directory, open a terminal and type `mkdir .pulse`. Then, `cd .pulse` and create a single file called client.conf, which looks like this:  

`autospawn = no`

That's all folks! Exit your chroot, crank it back up, and pulseaudio will be "dead".  

Afterwards, you can control pulseaudio with `pulseaudio --start` and `pulseaudio --kill`.

Conversely, if you need pulseaudio to work (for example for Firefox), you may need to create file `$HOME/.config/pulse/client.conf` with the single line:

`autospawn = yes`

This will ensure that the pulseaudio daemon gets started when you need it for any program that depends on it!