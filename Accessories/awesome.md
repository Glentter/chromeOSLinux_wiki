Mon Jan 23 13:38:16 PST 2023
## awesome works fine in crouton (added a bash script for this: [[get-crouton-awesome.sh]]):
* Install xorg: `sudo crouton -t xorg `

    If there are existing target, then use update: `sudo crouton -u -t xorg`

* Enter the chroot: `sudo enter-chroot -n chrootname` (name is perhaps `precise`)
* Install awesome: `sudo apt-get install awesome`
* Add `exec awesome` to ~/.xinitrc: `echo "exec awesome" > ~/.xinitrc`
* Create configuration directory `mkdir -p ~/.config/awesome`
* Copy default configuration from /etc/xdg/awesome: `cp -r /etc/xdg/awesome/* ~/.config/awesome/`
* Launch awesome directly from the crosh shell: `sudo enter-chroot xinit`
  (for convenience, add an alias: alias startawesome='sudo enter-chroot xinit' in the ~/.bashrc)

* On Chromebooks, the search key (right below tab) is actually the 'Mod4' key. awesome works with it right out of the box!