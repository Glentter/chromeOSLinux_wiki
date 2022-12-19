This cool trick is not covered anywhere. Type in these commands from the shell:  
  
    cd /usr/local  
    ls -l  

You will notice a shortcut, or link, to the location of your chroots. Here is what mine looks like:  
  
    chronos@localhost /usr/local $ ls -l  
    total 4  
    drwxr-xr-x. 2 root root 4096 Jul 27 19:52 bin  
    lrwxrwxrwx. 1 root root   50 Jul 29 16:46 chroots -> /media/removable/Storage/sevak/Chromebook/chroots/  
    chronos@localhost /usr/local $   

This is not what yours will look like! I have changed the location where my chroots will be installed to a subfolder of my home directory in a dual-boot install! There are so many cool things I can do with this.

If you have existing chroots, don't try this until you have moved them to the new location. Disclaimer: moving chroots is tricky and can completely screw them up.

Crouton will install your chroots to wherever this shortcut, or link, points. Here is the code:  
  
    cd /usr/local  
    sudo rm chroots  
    sudo ln -s /foo/chroots/ chroots

"foo" is an accepted placeholder for whatever goes in its place. The folder doesn't even have to be named chroots, just the link does. Here is an example using the Downloads folder:  

    cd /usr/local  
    sudo rm chroots  
    sudo ln -s ~/Downloads/MyChroots/ chroots

Happy hacking!!!