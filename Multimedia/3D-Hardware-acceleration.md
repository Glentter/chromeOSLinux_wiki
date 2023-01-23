Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
All Chromebooks provide some 3D hardware acceleration in Chrome OS. However, experience with crouton chroot varies depending on the release chosen and the type of hardware.

### Haswell/Bay Trail-based Chromebooks
(e.g. Acer C720 `peppy`, ASUS Chromebook C200 `squawks`)

Haswell/Bay Trail-based Chromebooks require updated drivers to support 3D acceleration.

Working releases:
* Ubuntu 12.04 (`precise`): crouton automatically install backported drivers ([#890](https://github.com/dnschneid/crouton/pull/890)).
* Ubuntu >=13.10 onwards (`saucy`/`trusty`).
* Debian `jessie`/`sid`

Releases that do not work:
* Debian `wheezy`: There are no backported drivers provided by Debian, so this will probably never be supported.
* `kali`: Based on `wheezy`, so no easy fix is available.

### Intel-based Chromebooks on kernel 3.4
(e.g. Acer C710 `parrot`; Cr-48 `x86-mario` is an exception, it works accross the board, as it uses a much older chip: `i915` driver)

OpenGL mesa libraries version >=9 require kernel >=3.6 to work on i965-based hardware (e.g. Sandy/Ivy Bridge). crouton includes a fix to use old mesa libraries on recent Ubuntu releases.

Working releases:
* Ubuntu 12.04 (`precise`)
* Ubuntu >=13.10 onwards (`saucy`/`trusty`): crouton automatically downgrades mesa libraries ([#913](https://github.com/dnschneid/crouton/pull/913)).
* Debian `wheezy`, and `kali`

Releases that do not work:
* Debian `jessie`/`sid`. See [this comment in issue 704](https://github.com/dnschneid/crouton/issues/704#issuecomment-39042864) for a workaround.

### Chromebook Pixel

Should work fine on all releases (it does not use kernel 3.4, nor is Haswell-based).

### ARM-based Chromebooks

Technically possible, but would require a lot of work and there are currently no guides on how to do it.

Most people just dual boot and use a mainline kernel with Mesa drivers (such as Panfrost) instead (vendor drivers only support GLES).

### Tools to check hardware acceleration support

Figure out the PCI id of the video device (see [PCI id database](http://pci-ids.ucw.cz/read/PC/)):
```
cat /sys/class/graphics/fb0/device/vendor
cat /sys/class/graphics/fb0/device/device
```

Check if hardware acceleration works:

 - `glxinfo`: `OpenGL vendor string` should be `Intel` on Intel hardware (not `VMWare`/`llvmpipe`)
 - `xdriinfo`: No error
 - `glxgears -info`: Gears are displayed and running

[Output on test machines](http://drinkcat.github.io/chroagh/2014-07-09_00-37-27_drinkcat_chroagh_x11test.tmp_35.html).

Note: `trusty` users will find `glxinfo` and `glxgears` in the `mesa-utils` package.