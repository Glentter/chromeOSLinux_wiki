Mon Jan 23 13:38:16 PST 2023
Quit downloading the same releases over and over. Here is a simple example, using 3 different releases:

    sudo sh ~/Downloads/crouton -d -f ~/Downloads/trusty.tar.bz2 -r trusty  
    sudo sh ~/Downloads/crouton -d -f ~/Downloads/xenial.tar.bz2 -r xenial  
    sudo sh ~/Downloads/crouton -d -f ~/Downloads/stretch.tar.bz2 -r stretch

Now here is how to install targets from each release:  

    sudo sh ~/Downloads/crouton -f ~/Downloads/trusty.tar.bz2 -t lxde,xfce,xorg,xiwi,keyboard  
    sudo sh ~/Downloads/crouton -f ~/Downloads/xenial.tar.bz2 -t lxde,xfce,xorg,xiwi,keyboard  
    sudo sh ~/Downloads/crouton -f ~/Downloads/stretch.tar.bz2 -t lxde,xfce,xorg,xiwi,keyboard

Note how you don't have to specify the release when using the file name, just the targets. This way, when you have to delete a chroot, you don't save to start from scratch to install it again.  
  
Have fun!