Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
# Installation

## 1. Enter Developer Mode

Hold Power+ESC+Refresh, then let go of the power, when the OS verification warning screen appears enter  Ctrl-D

OR go to Settings >> About --- OS >> Detailed Build info >> Change Channel

## 2. Setup Crouton

Follow the instructions here to download the latest version of https://goo.gl/fd3zc.

Open a chrome terminal tab and then open a shell

    shell

Figure out what version of linux you want to run here https://github.com/dnschneid/crouton

Install the crouton extension in chromeos from here https://goo.gl/OVQOEt

## 3. Setup a chroot
Install Crouton using this command

    sudo install -Dt /usr/local/bin -m 755 ~/Downloads/crouton

I would reccomend running Ubuntu Unity, Gnome 3 and enlightenment using an Ubuntu Trusty Tahr release.

    sudo crouton -t keyboard,extension,unity -r trusty -n unitytrusty
    sudo crouton -t keyboard,extension,gnome -r trusty -n gnometrusty
    sudo crouton -t keyboard,xiwi,e17 -r trusty -n enlightenment
    
After the instalation is complete you will set up your unix username and password for the chroot in the terminal.

Once the chroot is done installing and you have set up your user you launch your chroot using:

    sudo startunity
    sudo startgnome
    
[Crouton Command Cheat Sheet](https://github.com/dnschneid/crouton/wiki/Crouton-Command-Cheat-Sheet)

## 4. Setup Lightweight Desktop

Click the dash icon in the top left corner and search for terminal, use xterm to install software using aptitude

    sudo apt-get install terminator chromium-browser bluefish arduino vlc git retext htop
        
Download Cura from http://software.ultimaker.com/

    cd Downloads
    sudo dpkg -i cura_15.02.1-debian_amd64.deb

Download and install Papyrus notes

    cd Downloads
    wget http://aseman.co/downloads/papyrus/1/papyrus_1.0.0-1-amd64.deb
    sudo dpkg -i papyrus_1.0.0-1-amd64.deb
    
Download and install Github Atom

    cd Downloads
    wget https://github.com/atom/atom/releases/download/v0.189.0/atom-amd64.deb
    sudo dpkg -i atom-amd64.deb
    
## 5. Update Keyboard to match ChromeOS

    xmodmap -e "keycode 94 shift = backslash bar"
    xmodmap -e "keycode 51 shift = numbersign asciitilde"
    xmodmap -e  "keycode 11 shift = 2 quotedbl"
    xmodmap -e "keycode 48 shift = apostrophe at"
    
    xmodmap -e "keycode 22 shift = BackSpace Delete"
    xmodmap -e "keycode 133 = Caps_Lock"
    
    
## * Optional - Install Non Free Software that makes things easier and better looking

    sudo apt-get install ttf-mscorefonts-installer

    sudo apt-get install ubuntu-restricted-extras
    
    
## Issues

+   The keyboard command used when installing the chroot don't do anything
+   The chrome extension with chroot chrome window functionality is not working
