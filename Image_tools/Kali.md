Mon Jan 23 13:38:16 PST 2023
[Kali](http://www.kali.org/) is a distro for information security folks, so you should be able to figure it out.  Here is some help to get started.

To install, download crouton and open a shell:

Hit `CTL - ALT - T`

Type `shell` at the prompt.

Enter the following command:

`sudo sh -e ~/Downloads/crouton -r kali-rolling -t xfce`

To install the old release, enter this:

`sudo sh -e ~/Downloads/crouton -r sana -t xfce`

xfce is the preferred desktop, use others at your own risk.

To enter your Kali chroot type the following:

`sudo enter-chroot -n nameofchroot `

or start with:

 `sudo startxfce4 -n nameofchroot `

Where nameofchroot is what you named it. (release name by default)

No tools are installed by default to save space.  You can easily install anything you need.  Here are some examples:

`sudo apt-get install nmap`

`sudo apt-get install metasploit-framework`

`sudo apt-get install nikto`

If you have enough space, you can install one of these Kali metapackages:

```
kali-linux-arm/kali-rolling
  Kali Linux ARM system

kali-linux-core/kali-rolling
  Kali Linux base system

kali-linux-default/kali-rolling
  Kali Linux default system

kali-linux-everything/kali-rolling
  Everything in Kali Linux

kali-linux-headless/kali-rolling
  Kali Linux default headless system

kali-linux-large/kali-rolling
  Kali Linux large system

kali-linux-nethunter/kali-rolling
  Kali Linux NetHunter tools
```

For example: 

`sudo apt-get install kali-linux-default`

If you are having issues, try harder, it's the hacker way.  If that doesn't work, use ubuntu.  ;)

Note: If the X server fails to start while using xfce target, removing the xserver-xorg-legacy package once in the chroot may fix the issue (restart the chroot after removing the package). This fixed my issue on the X server error so I thought I'd include the command I ran based off this note:

 `sudo apt-get remove xserver-xorg-legacy`

I restarted the chroot and xfce started up fine.