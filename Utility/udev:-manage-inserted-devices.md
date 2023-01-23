Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
udev performs rule-defined actions as a result of device events. For example, it can change the permissions of device nodes, to allow access to devices as a normal user.

### Leverage on Chromium OS' udev daemon (recommended)

udev is already running in Chromium OS, and we can use it.  To do so,

* Put files containing (only) the rules you need in `/etc/udev/rules.d` in the chroot.

* In `/etc/rc.local`, add the lines

        mkdir -p /run/udev/rules.d
        cp /etc/udev/rules.d/*.rules /run/udev/rules.d
        udevadm control --reload

(`/run/udev` is bind-mounted to the appropriate Chromium OS directory.)

#### Sample rules

Note that user and group names specified in rules will be matched to the Chromium OS `/etc/passwd` and `/etc/group` files rather than the chroot's.  The chroot's numerical values can be used instead, e.g. `GROUP="1234"` in the example above.

The default rules used by Chromium OS can be found in `/lib/udev/rules.d` (outside the chroot). Using the same filename in `/etc/udev/rules.d` (inside the chroot) will override the rule in `/lib/udev/rules.d`. It is recommended to choose a different name, starting in `999-`, so it gets executed after Chromium OS rules.

##### Security key devices

[Security keys](http://googleonlinesecurity.blogspot.com/2014/10/strengthening-2-step-verification-with.html) can be used in the chroot, with Chrome/Chromium release >= 38.

First, figure out your USB vendor/product id from the output of `dmesg`, right after plugging in the device (`lsusb` can also provide this information):

    [...] usb 1-1.2: new full-speed USB device number 45 using ehci_hcd
    [...] usb 1-1.2: New USB device found, idVendor=2581, idProduct=f1d0

In this case, vendor/product ids are `2581` and `f1d0`.

Then create `/etc/udev/rules.d/999-securitykey.rules` with the following content, updating the vendor/product fields as required:

    SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="f1d0", OWNER="root", GROUP="devbroker-access", MODE="0660"

Finally, add your user to the `devbroker-access` group:

    sudo usermod -G devbroker-access -a $USER

Then restart your chroot.

Do not attempt to use another group, as Chromium OS will change the device group when it is used from Chromium OS, blocking its use from crouton.

##### Other devices

See udev documentation (e.g. http://reactivated.net/writing_udev_rules.html) for more information.

### Running a second udev daemon in the chroot

The udev daemon can be run inside a crouton chroot by including `/etc/init.d/udev start` in `/etc/rc.local`.  However, this can cause conflicts, e.g. the default udev rules make crouton's audio fail.  This is not advised.

