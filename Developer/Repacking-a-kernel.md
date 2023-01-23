Mon Jan 23 13:38:16 PST 2023
This section provides full instructions about rebuilding a kernel, not just changing the boot options. Thanks to juancferrer.

1.Open a shell on your Chrome OS

**2.Disable verified boot :**
(You could do this later, but you have to do this before step 10. It won't boot up otherwise.)
>**sudo crossystem dev_boot_signed_only=0**

3.Change current directory to `Downloads` as a temporary folder:
> cd ~/Downloads/

4.Copy the existing kernel into a file:
>sudo dd if=/dev/sda2 of=kernel2<br>
>sudo dd if=/dev/sda4 of=kernel4

5.Get the kernel configuration:
>vbutil_kernel --verify kernel2 --verbose | tail -1 > vmxoff-config2.txt<br>
>vbutil_kernel --verify kernel4 --verbose | tail -1 > vmxoff-config4.txt

6.At this point you change the configuration. For example to enable VT_x (see also [[Enable kernel VT_x for Virtualbox]]) add 'disablevmx=off' to the config line:
>sed -i -e 's/$/ disablevmx=off/' vmxoff-config2.txt<br>
>sed -i -e 's/$/ disablevmx=off/' vmxoff-config4.txt

7.Repack the kernels:
>vbutil_kernel --repack repacked2 
    --signprivate /usr/share/vboot/devkeys/kernel_data_key.vbprivk 
    --keyblock /usr/share/vboot/devkeys/kernel.keyblock 
    --oldblob kernel2 
    --config vmxoff-config2.txt

>vbutil_kernel --repack repacked4 
    --signprivate /usr/share/vboot/devkeys/kernel_data_key.vbprivk
    --keyblock /usr/share/vboot/devkeys/kernel.keyblock
    --oldblob kernel4
    --config vmxoff-config4.txt

8.Verify the new kernels (You should see `Body verification succeeded`):
>vbutil_kernel --verify repacked2 --verbose<br>
>vbutil_kernel --verify repacked4 --verbose

9.Copy the kernels back:
>sudo dd if=repacked2 of=/dev/sda2<br>
>sudo dd if=repacked4 of=/dev/sda4

10.Reboot, and enjoy VT-x extensions