Once you have VirtualBox installed, you may notice that you are unable to access USB devices. This is because udev is running outside the crouton, and VBox is inside. Before this, you should get udev working based on [this wiki page](https://github.com/dnschneid/crouton/wiki/udev:-manage-inserted-devices).

For those who don't know, Virtual Box handles USB access like so:  
1. USB device plugged in  
2. Udev rule (virtualbox.rules) is activated  
3. Runs VBoxCreateUSBNode.sh (in /lib/udev/)  
4. Creates /dev/vboxusb folder structure with USB device nodes  
5. Sets permissions and root:vboxusers ownership  
6. Virtual Box reads /dev/vboxusb  

So there are 2 issues. First, udev expects the script at `/lib/udev/VBoxCreateUSBNode.sh`, it is at `/run/crouton/.../lib/udev/VBoxCreateUSBNode.sh`
To fix this, copy the file out of the chroot.

(For certain packages the path may be `/usr/share/virtualbox/VBoxCreateUSBNode.sh`)

From inside the crouton

    $ cp /lib/udev/VBoxCreateUSBNode.sh ~/Downloads/

or (depending on the location of the file)
 
    $ cp /usr/share/virtualbox/VBoxCreateUSBNode.sh ~/Downloads/

Then from outside the chroot (chrome OS shell)  

    $ sudo mv ~/Downloads/VBoxCreateUSBNode.sh /usr/local/
    $ sudo chmod 755 /usr/local/VBoxCreateUSBNode.sh
    $ sudo chown root:root /usr/local/VBoxCreateUSBNode.sh

The second issue is that the group vboxusers does not exist outside the crouton, so we must set the group ownership based on GID, not group name.

First, find the group ID of vboxusers

    $ getent group vboxusers | awk -F: '{printf "Group %s with GID=%d\n", $1, $3}'
    Group vboxusers with GID=126

The 3-digit number is the GID. Mine was 126.

Download this <https://gist.githubusercontent.com/Timvrakas/d43fe840d9fc336cae7b/raw/virtualbox.rules>
and copy it to /etc/udev

    $ sudo cp ~/Downloads/virtualbox.rules /etc/udev/rules.d/
    $ sudo rm /etc/udev/rules.d/*-virtualbox.rules

Now edit that file

    $ nano /etc/udev/rules.d/virtualbox.rules

change the 3-digit number at the end of the first two lines to the GID (ex: 126)

Reload udev with newly created rule

    $ udevadm control --reload

Finally, add yourself to the `vboxusers` group.

    $ sudo adduser `whoami` vboxusers