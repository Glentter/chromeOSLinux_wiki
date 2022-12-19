This page collects the most relevant tips and tricks for using crouton on an Acer C710, and it's many model variations.  Specifically these tips were from setting up crouton with Ubuntu with the Unity Desktop environment on Trusty 14.04, but they should apply to a wide variety of installs on this hardware.  

This page is based off the [C720 page](https://github.com/dnschneid/crouton/wiki/Acer-C720-C720P) and will be slowly updated and corrected over the summer of 2014.

### Hardware acceleration in Chromium
Open chrome://flags and enable "Override software rendering list." You can check the status of hardware acceleration in chrome://gpu

It is significant to note that the C710 models use the PARROT firmware, which relies on the 3.4 Linux kernel. This means that hardware acceleration from drivers requiring the 3.6 or higher kernel won't work. This has been addressed in issues 704 and 913. You can [see here](https://github.com/dnschneid/crouton/wiki/3D-Hardware-acceleration#intel-based-chromebooks-on-kernel-34) for more information. 

### UNEDITED CONTENT BELOW. BRING EDITED CONTENT ABOVE THIS SECTION

### Touchpad
Install the ChromeOS driver.  Since you're keeping ChromeOS by using crouton, it makes sense that the touchpad should perform the same in both environments.  You could spend a lot of time playing with the synaptic driver, but I don't see the benefit, personally.
http://www.reddit.com/r/chrubuntu/comments/1rsxkd/list_of_fixes_for_xubuntu_1310_on_the_acer_c720/cf82pk4

### External mouse
Using the ChromeOS touchpad driver results in losing your ability to use an external mouse.  This can be fixed.
http://www.reddit.com/r/chrubuntu/comments/1rsxkd/list_of_fixes_for_xubuntu_1310_on_the_acer_c720/cg0wian

### Power button
By default, hitting the <kbd>⏻</kbd> button will result in the laptop shutting down without warning.  [This fix](https://askubuntu.com/questions/363399/xubuntu-13-10-shuts-down-without-asking-when-power-button-pressed) will ensure that you get a popup that offers you the standard shutdown/suspend/logout options.

### Other hardware keys
Most tips on this show you how to make the hardware keys work like the symbols on them.  The only downside to that is that you can't get the underlying functions... <kbd>F1</kbd> for help, for example.  I elected to make the hardware buttons work along with the Search button.  If you prefer it the other way, it should be clear from this setup how to remove the modifier key.
http://www.reddit.com/r/chrubuntu/comments/1rsxkd/list_of_fixes_for_xubuntu_1310_on_the_acer_c720/cgb2x3s
