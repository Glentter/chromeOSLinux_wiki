### You can use an [extension](https://github.com/supechicken/ChromeOS-AutoStart) to do it without removing the rootfs verification now 🎉

### Here is the original text

### Method and scripts to invoke a crouton chroot session at ChromeOS startup.

----------
#### **Step 1.** 
If not already done you first need to make the root file system read-writable by **REMOVING** 'rootfs verification' *and* **rebooting**. <br>  
You can do that in a CrOS shell (crosh) by running this new & easier method:   
```
sudo /usr/libexec/debugd/helpers/dev_features_rootfs_verification
```    

Now, make sure you **reboot** to put 'rootfs' in a read-write state. 
  
#### **Step 2.** 
Next, you have to place a custom config script in the ```/etc/init``` folder. <br>
This script, '[crouton.conf](https://gist.github.com/DennisLfromGA/6443733)', is the one that will kick-off a crouton chroot without having to open a shell manually at ChromeOS startup (ta-da!). <br>

 - To obtain the file `crouton.conf`, in a CrOS shell (crosh) do:
```
curl -Lk --connect-timeout 60 -m 300 --retry 2 "https://gist.githubusercontent.com/DennisLfromGA/6443733/raw/crouton.conf" -o ~/Downloads/crouton.conf
```
 - To place `crouton.conf` in `/etc/init/`, in a CrOS shell (crosh) do:    
```
sudo cp ~/Downloads/crouton.conf /etc/init/
```

[ See Note 1. below for making changes to this file. ]

  
#### **Step 3.** 
Next, you can place a file named '[crouton.init](https://gist.github.com/DennisLfromGA/aa1c92ebe77c3df4ca84)' in your ```~/Downloads``` folder or the chroot parent folder ( `/usr/local/` by default ) for external control.  
If, for some reason, you choose *not* to use the external control file '[crouton.init](https://gist.github.com/DennisLfromGA/aa1c92ebe77c3df4ca84)', then you may need to edit the CHROOT, START_DE, and possibly other variables in `/etc/init/crouton.conf` but this method is for *powerusers* only ;) and is *not* recommended.
 - You can roll your own or, for one with lots of examples, you can obtain the file `crouton.init`, in a CrOS shell (crosh) do:
```
curl -Lk --connect-timeout 60 -m 300 --retry 2 "https://gist.githubusercontent.com/DennisLfromGA/aa1c92ebe77c3df4ca84/raw/crouton.init" -o ~/Downloads/crouton.init
```
 - This file can stay in `~/Downloads` when using the default *starting point*.
 - You *can* place it in `/usr/local/`, for starting before logging in for example, in a CrOS shell (crosh) do:
```
sudo cp ~/Downloads/crouton.init /usr/local/
```
[ See Note 2. below for making changes to this file. ]
  
#### **Step 4.** 
Finally, **reboot** once more to implement the changes and you're good to go...

----------
## Additional notes and information

#### **Note 1.**
The '[crouton.conf](https://gist.github.com/DennisLfromGA/6443733)' file, installed in ```/etc/init```, allows you to start a crouton chroot session at one of four different points in the boot/startup/login process as shown below:
```
#start on starting ui          # 1st - starts when the user interface begins
                               #     + only 1 chroot will run in this mode
                               #     + must use XMETHOD: 'xorg'
 
#start on started ui           # 2nd - starts when the user interface appears
                               #     + only 1 chroot will run in this mode
                               #     + must use XMETHOD: 'xorg'
 
#start on login-prompt-visible # 3rd - starts when the login screen appears
                               #     + only 1 chroot will run in this mode
                               #     + must use XMETHOD: 'xorg'
 
 start on start-user-session   # 4th - starts when the user logs in - **DEFAULT**
                               #     + multiple chroots could run in this mode
                               #     + can use either XMETHOD: 'xorg' or 'xiwi'
                               #     + ensures user ~/Downloads is available
```
You'll note that the last or 4th option is not prefixed with a '#' and is the default since it will start after you're logged in so that you can use your ```~/Downloads``` folder in both your chroot and in ChromeOS like you normally would in crouton, it also allows you to use either *installed* XMETHOD: 'xorg' or 'xiwi'.

 - If you want to start your crouton chroot earlier in the boot/startup/login process, just remove the remark character '#' in the first column of the option you want to use, then be sure to prepend it in the option you no longer want.    
NOTE: *Use only one startup point, always prepend the others with the remark character: #*

Modifying this file to start at one of the three startup points, other than the last/default startup point, is the only change that *may* need to be made to this file. The other options will be set in the `crouton.init` file.
  
#### **Note 2.**
There are now six parameters that are set by default ( in 'crouton.conf' ) but can be edited and changed by the external control file 'crouton.init'.  
These parameters are:
```
 1) DELAY=10  
 2) CHROOT=xenial  
 3) START_DE=startxfce4 
 4) CHROOT_APP=none
 5) XMETHOD=default  
 6) RUN_STATE=y 
```

The '[crouton.init](https://gist.github.com/DennisLfromGA/aa1c92ebe77c3df4ca84)' file, placed either in the ```~/Downloads``` folder or the chroot's parent folder, allows you to adjust the same six variables without changing the above mentioned config file each time.  
The parameters are:
```
 1) DELAY - in seconds, default is '30'
 2) CHROOT - installed chroot name, default is 'xenial'
 3) START_DE - crouton start* script, default is 'startlxde' 
 4) CHROOT_APP - enter desired chroot application to run, default is 'none'  
 5) XMETHOD - Override the default XMETHOD, default is 'xiwi'
 6) RUN_STATE - use 'n' for No, default is 'y' for Yes
```
Note that the above values in the downloaded 'crouton.init' file are different than those found in the 'crouton.conf' file, they are adjusted for one specific scenario but they can be edited to suit your needs.  
Also, *please* note that the downloaded 'crouton.init' file contains many examples for many different scenarios but only the lines that are **not** prefixed by a '#' (comment) will be used/read.

Please make sure you adhere to the format currently used in the 'crouton.init' file, comments are okay but the variables have to start in the first column.

----------
## **Caveats to this approach:** <br>
1. These changes need to be done *outside* your chroot in a CrOS shell (crosh). <br>
2. *Each time you get a new ChromeOS update*, you have to again remove rootfs verification and place a copy of the 'crouton.conf' and, optionally, the 'mnt-crouton.conf' file in ```/etc/init``` as shown in Step 1.<br>
3. __This will *not* work on encrypted chroots since they require a password to be entered.__