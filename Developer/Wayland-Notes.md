Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
The intent of this page is to collect notes on using the ChromeOS wayland server from chroots.

## Xwayland as a full-screen window (tested on xenial, ARM) ##
### The how
 - Install xwayland (apt-get install xwayland) and other X packages you want to run  
 - Setup an .xinitrc for your desktop  
 - host-wayland /usr/bin/xinit -- /usr/bin/Xwayland
### The why or what
You need to use full path to xinit because crouton's xinit wrapper would otherwise interfere.  
Clipboard syncing doesnt work, might if you run the crouton solution for that, not tested.  
Keyboard layouts work out of the box, even switching them in ChromeOS gets passed all the way to X.  
Alt key gets stuck when you Alt-Tab in/out, remember to touch Alt after getting back in or weird things might happen.  
Changing the X window size doesnt seem to be possible, atleast i didnt find a way. You can move it to a different screen with the Search-Alt-Arrows keys but it will stay the size of the primary display.

## mpv (media player) wayland backend
$ host-wayland mpv -vo wayland  
There is no automatic rescaling or auto-fullscreen it seems, but you can use --autofit (eg. --autofit=1280 or adjust your screen width).

## Qt applications ##
 - Install qtwayland5 (apt-get install qtwayland5) and the Qt program you intend to run
 - host-wayland *appname* -platform wayland

Tested with qterminal

## weston (wayland backend) with xwayland plugin
 - install xwayland and weston
 - host-wayland weston --backend=wayland-backend.so -Swayland-1 --modules=xwayland.so