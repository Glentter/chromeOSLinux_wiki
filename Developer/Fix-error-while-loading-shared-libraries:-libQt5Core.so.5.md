error while loading shared libraries: libQt5Core.so.5: cannot open shared object file: No such file or directory
***
This error appears to occur when running any Qt5-based program in a very current chroot, e.g. Debian Buster. The problem is deeper than I can comprehend, but it has to do with Chromebook's kernel version.

Go the shell, and type

`uname -r`

For my Acer Chromebook 11 C740, I get 3.14.0

The kernel needs to be at least 3.15 for a very real but (to me) esoteric reason: "Qt 5.10 uses the renameat2 system call which is only available since kernel 3.15". If you want to read a very long thread, look here:

[https://bbs.archlinux.org/viewtopic.php?id=232682](https://bbs.archlinux.org/viewtopic.php?id=232682)

You need not understand the inner workings of a car to drive one (thank goodness), nor do you need to understand the intricacies of this error to fix it:

`sudo strip --remove-section=.note.ABI-tag /usr/lib64/libQt5Core.so.5`

The above command "strips out" a piece of code which is looking in the kernel for something not there, and allows the module to load.

Read more:

[https://superuser.com/questions/1347723/arch-on-wsl-libqt5core-so-5-not-found-despite-being-installed](https://superuser.com/questions/1347723/arch-on-wsl-libqt5core-so-5-not-found-despite-being-installed)

Good Luck running Buster. It seems surprisingly stable while giving very current versions of packages.

~James

Experienced this problem when installing FreeCAD and LibreCAD on an old Chromebook HP G4 running Kali (kernel 3.8).<br/>The solution in my case was:<br/>`sudo strip --remove-section=.note.ABI-tag /usr/lib/x86_64-linux-gnu/libQt5Core.so.5`<br/>
-jernic

This fixed the error

`error while loading shared libraries: libQt5Core.so.5: cannot open shared object file: No such file or directory`

when running `systemdgenie` as part of an [ArchWSL install](https://github.com/yuk7/ArchWSL/wiki/Known-issues#systemdsystemctl). Thanks. 
- thomasn
