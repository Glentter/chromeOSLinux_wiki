So far, it looks like even a novice can get linux going on the ASUS C200 (4G 32G-SSD). I'm offering these comments in case they help others who want to take the plunge and turn their ASUS C200 into a portable linux machine.

The only problem I seemed to have when installing was the software center. I noticed an error message that included "software center failed to setup dbs". And once I was on the desktop the software center was missing stuff (e.g. LibreOffice and Wine).  But that was easy to fix, after googling I found these commands which fixed the software center:    sudo apt-get update     and then   sudo apt-get install software-center*     

In case it helps other rookies: after a powerwash [esc-refresh, then touch power button; ctrl-d twice] , I got into developer's mode (15 minutes), then got the crouton extension [crouton integration at google play]  and downloaded crouton . To install ubunutu, I used 
 sudo sh ~/Downloads/crouton -e -r trusty -t xfce,xfce-desktop,xiwi 
I was unsure about using the commas for multiple targets, but it seemed to work.

Getting skype to work was a chore; in software manager Edit/Software Sources/Other Software/Add
deb http://archive.canonical.com/ubuntu trusty partner  
After that I tried sudo-apt update  -- no sign of skype   and then  sudo-apt upgrade   -- still no sign of skype
But then I tried to install the command line and it worked.   sudo apt-get install skype

Sometimes we want fullscreen instead of running in a tab (no hardware acceleration), so from the crouton command sheet we have:  Switch between xorg and xiwi if both installed: sudo startxfce4 -X xiwi or sudo startxfce4 -X xorg  

I figured I did not have xorg installed (it was not one of my targets), so I got it using an update. First I 
backed up my chroot        sudo edit-chroot -b trusty      which took quite a while
then I did the update/add new target: sudo sh ~/Downloads/crouton -u -n trusty -t xorg        I bet there was a way to simply add the target to my chroot (I should have tried sudo sh ~/Downloads/crouton -n trusty -t xorg )

that took a while (lots of messages saying it was already the latest version), but I did end up being able to do either  sudo startxfce4 -X xiwi or sudo startxfce4 -X xorg

A final point for now: I just stumbled on the advice  "Stay away from the Ubuntu Software manager, and use the command line. See Ubuntu's AptGet/Howto."  I wonder if that means stay away from Ubuntu's Software Center? hmm ...


