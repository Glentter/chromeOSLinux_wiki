So, this page will cover 2 annoying issues with lxde.
the first one is the lxsession: cannot open display.
the second one is the pid bug ( you can just click ok and get on with your day)





now then, for the first bug, this work around is really simple. 

Just enter-chroot as usual, and once in, just run the following line of code:
```sh
exec xinit /usr/bin/startlxde
```


It's... that simple.