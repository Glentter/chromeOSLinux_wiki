[VT_x](http://en.wikipedia.org/wiki/X86_virtualization#Intel_virtualization_.28VT-x.29) is a virtualization technology that comes in handy for example in case you want to run [VirtualBox](https://www.virtualbox.org/).

1. commands described here need to be executed in a ``crosh`` root shell (use ``sudo su`` to become root)
2. after a ChromeOS update you will need to do this again
3. instead of ``/dev/sda``, your Chromebook storage might instead be ``/dev/mmcblk0``, use this accordingly

To fully repack a kernel, see [[Repacking a kernel]].

Foreword on kernels
===================
For more information about ChromeOS partitions see [chromiumos-design-docs/disk-format](http://www.chromium.org/chromium-os/chromiumos-design-docs/disk-format).

In short, kernels are stored in partitions and have an associated priority which is a number between 0 and 15, where 0 means kernel is not bootable; a higher number corresponds to a higher boot priority.
The kernel with the highest priority will be booted first and in case of non-signed kernel or failure the next one will be picked up. If no valid kernels are available, you will need to reinstall ChromeOS (or if you can boot from USB you can restore a valid kernel that way).

Enabling VT_x
=============

In order to enable VT_x we will modify the configuration of the currently highest priority kernel; this will be possible only if verified boot is disabled:
```
$ crossystem dev_boot_signed_only=0
```

**NOTE**: ALSO SET THE BELOW ELSE CROUTON WILL SET dev_boot_signed_only to 1 again.
```
$ crossystem dev_boot_usb=1 dev_boot_legacy=1
```

Before proceeding it is necessary to know which kernel partition will be used at boot.

Identify partition number:
```
$ cgpt show /dev/sda -qn | grep 'FE3A2A5D-4F32-41A7-B725-ACCC3285A309$' | awk '{ print $3 }'
2
4
6
$ 
```
These are the partition numbers of potentially bootable kernels; identify their priority with:
```
$ for N in `cgpt show /dev/sda -qn | grep 'FE3A2A5D-4F32-41A7-B725-ACCC3285A309$' | awk '{ print $3 }'`; do echo -n "Partition $N has priority "; cgpt show /dev/sda -i $N -P; done | sort -r -k5
Partition 4 has priority 2
Partition 2 has priority 1
Partition 6 has priority 0
$
```

**NOTE:** if you get GPT_ERROR_INVALID_HEADERS errors then you need to reboot, this is probably your first boot

Let's say our kernel with the highest priority will be kernel B on partition 4; if yours is different then replace the ``$P`` in the next example commands with your specific value.
```
export P=4 ## partition number of the highest priority kernel
```

Save the config of kernel B which is on partition $P:
```
$ /usr/share/vboot/bin/make_dev_ssd.sh --save_config /tmp/kconfig --partitions $P
Saving Kernel B config to /tmp/kconfig.4
(Kernels have not been resigned.)
$
```
The file ``/tmp/kconfig.4`` can be edited to add `disablevmx=off` to enable VT_x; if you also want to disable module_locking so you are able to load modules outside the rootfs you need to also add `lsm.module_locking=0`.

Once your changes to the kernel config file are complete it can be set on the kernel partition with:
```
$ /usr/share/vboot/bin/make_dev_ssd.sh --set_config /tmp/kconfig --partitions $P
Kernel B: Replaced config from /tmp/kconfig.4
Backup of Kernel B is stored in: /mnt/stateful_partition/backups/kernel_A_20150208_223837.bin
Kernel B: Re-signed with developer keys successfully.
Successfully re-signed 1 of 1 kernel(s)  on device /dev/sda.
```

At this point you're finished; provided that you have disabled verified boot as per first step, you can boot the new kernel.