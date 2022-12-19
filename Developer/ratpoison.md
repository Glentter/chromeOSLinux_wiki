Ratpoison is a very fast, low resource, lightweight Window Manager with very few or no library dependencies, and no fancy window decorations. It is a nice alternative to i3, awesome, or other tiling window managers, particularly for those using older hardware, such as older netbooks or low resource chromebooks.

It is very easy to install and set up Ratpoison in your chromebook using crouton

# Using Ratpoison in crouton 

1. Create a chroot with X11:  
`$ sudo sh ~/Downloads/crouton -r sid -t x11 `
2. Enter the chroot:  
`$ sudo enter-chroot -n sid`
3. Install ratpoison:  
`$ sudo apt-get install ratpoison ratmenu`
4. Add `exec ratpoison` to ~/.xinitrc:  
`$ echo "exec ratpoison" > ~/.xinitrc`
5. Launch Ratpoison directly from the crosh shell:  
`$ sudo enter-chroot xinit`
6. Create an alias for starting in Ratpoison:  
add `alias startratpoison='sudo enter-chroot -n sid xinit'` to your `/home/chronos/user/.bashrc` (chromeos)

### Helpful configurations

* Remember, all keystrokes with ctrl-t -- i.e., holding down the control key and pressing "t".
* Ctrl-t n shifts to the next window. Ctrl-t p shifts to the previous window. Users of `screen' will find keybindings to be intuitive. For example, ctrl-t s splits the current frame into two vertical ones (ctrl-t S (capital S) splits it into two horizontal ones). Ctrl-t Q makes the current frame the only one.
* In the event that you want to abort out of a ctrl-t, press ctrl-g
* Ctrl-t ? will bring up a brief list of sequences
* A quick tutorial for Ratpoison Keybindings can be found at https://en.wikibooks.org/wiki/Using_Ratpoison/Your_first_steps


----

