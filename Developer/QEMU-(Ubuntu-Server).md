Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
Despite crouton not supporting KVM for Virtualization, it is still a proper platform for using QEMU. It can run with Bart PE and [Rasbian Images](http://www.raspberrypi.org/phpBB3/viewtopic.php?f=29&t=37386), install Windows XP, and new from the labs, Ubuntu Server, regardless of the supporting architecture.

First thing we need is an iso of [Ubuntu Server](http://www.mirrorservice.org/sites/releases.ubuntu.com//raring/ubuntu-13.04-server-amd64.iso), as your standard live Desktop will not cut it.  QEMU and QEMU Launcher would be the supported programs on my end, but feel free to be flexible. Defaults settings will work on QEMU, and should work fine for the Ubuntu Install, so long as you press F4 at the "Install Ubuntu Server" splash screen, then enter and proceed as normal.

![Something like this.](http://static.howtoforge.com/images/perfect_server_ubuntu_13.04_nginx_bind_dovecot_ispconfig_3/big/2.png)

It is going to be a long and hard journey, and if the install fails, you have to start over from the last completed step. Could take as long as an entire day, but it is the only way. [Or use my premade image.](https://dl.dropboxusercontent.com/u/24460824/Server) Like with the Raspbian image, forwarding 5022 to 22 will get you networking in the virtual machine. While you may be better off with running off a proper install of Ubuntu on your local hard drive or external USB through Chrubuntu, but if you need a Virtual Machine, this could tide you over.