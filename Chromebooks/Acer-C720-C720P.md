Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
This page collects the most relevant tips and tricks for using crouton on an Acer C720/C720P.  Specifically these tips were from setting up crouton with xubuntu and saucy, but they should apply to a wide variety of installs on this hardware.  Most of these came from the reddit thread "List of fixes for Xubuntu 13.10 on the Acer C720", which is focussed towards a complete OS replacement, not a side-by-side install like you have with crouton.  As a result, many of the tips aren't necessary, and some outright will not work.

### Touchpad
Install the ChromeOS driver.  Since you're keeping ChromeOS by using crouton, it makes sense that the touchpad should perform the same in both environments.  You could spend a lot of time playing with the synaptic driver, but I don't see the benefit, personally.
https://github.com/hugegreenbug/xf86-input-cmt
https://launchpad.net/~hugegreenbug/+archive/cmt

An example script to build and install hugegreenbug's xf86-input-cmt driver for the Acer C720P on a Debian (Jessie) chroot is here: https://gist.github.com/darcyparker/3d89e7851fc10992000e

### External mouse
Using the ChromeOS touchpad driver results in losing your ability to use an external mouse.  This can be fixed.
http://www.reddit.com/r/chrubuntu/comments/1rsxkd/list_of_fixes_for_xubuntu_1310_on_the_acer_c720/cg0wian
**Note**: This is now fixed

### Hardware acceleration in Chromium
Open chrome://flags and enable "Override software rendering list." You can check the status of hardware acceleration in chrome://gpu

### Power button
By default, hitting the Power button will result in the laptop shutting down without warning.  This fix will ensure that you get a popup that offers you the standard shutdown/suspend/logout options. Simply edit `/etc/systemd/logind.conf` to make `HandlePowerKey=ignore` and you should be set! [Source](http://askubuntu.com/questions/363399/xubuntu-13-10-shuts-down-without-asking-when-power-button-pressed)
**Update**: I am in this situation and the fix has no effect. Instead, in xfce4 open `Session and Startup` and check `Prompt on logout` to get the dialog.

### Other hardware keys
Most tips on this show you how to make the hardware keys work like the symbols on them.  The only downside to that is that you can't get the underlying functions... F1 for help, for example.  I elected to make the hardware buttons work along with the Search button.  If you prefer it the other way, it should be clear from this setup how to remove the modifier key.
http://www.reddit.com/r/chrubuntu/comments/1rsxkd/list_of_fixes_for_xubuntu_1310_on_the_acer_c720/cgb2x3s