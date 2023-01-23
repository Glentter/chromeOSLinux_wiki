Mon Jan 23 13:38:16 PST 2023
The below instructions carry a  downside: you may be replacing a distro's firmware with older versions. In most cases, all you need is 2 files from the Chromebook, without which the mic of your internal soundcard simply will not work correctly. This is specific to each Chromebook, so I can only show you an example for an Acer Chromebook 11 C740:

From the shell, `cd /etc/modprobe.d` then `ls -l`. I find a file called alsa-auron_paine.conf. After executing `cat alsa-auron_pain.conf`, I see this:

options snd_hda_intel model=auron_paine patch=,auron_paine_alsa.fw

Ah ha!!! Gotcha!!! You will need to copy alsa-auron_paine.conf to /etc/modprobe.d, and auron_pain_alsa.fw to /lib/firmware.

After this, reboot your Linux install and use alsamixer to configure your sound card: the built-in mic now works!!!


***


Where can you find the firmware you need to do a full install of Linux on your Chromebook? From the Chromebook itself! Especially the sound card...

The following may be considered experimental. I have found it to work on my Acer Chromebook 11 C740, using Debian Stretch, as well as Lubuntu 14.04.1, 16.04.1, and 18.04.1.

For the purposes of this tutorial, insert a 16 GB USB stick or MicroSD card in your Chromebook and format it in File Manager. After it formats, rename it MyStuff.

Then, from the Chrome shell, execute the following 2 commands:

`cp -R /lib/firmware/ /media/removable/MyStuff/` and

`cp -R /etc/modprobe.d/ /media/removable/MyStuff/`

Now install your Linux on the built-in SSD or any other location. For the purposes of this tutorial, assume the user's name is james. Log into your now-installed Linux OS, and from a terminal, execute the following 2 commands:

`sudo cp -R /media/james/MyStuff/firmware/* /lib/firmware/` and

`sudo cp -R /media/james/MyStuff/modprobe.d/* /etc/modprobe.d/`

Now shutdown and reboot your full Linux install. Try configuring your sound card using alsamixer.

This technique also works if you're attempting to install the Official Debian netinst.iso: when you reach the point where the network hardware cannot be found, insert your memory stick and Debian will find it. Then, after the install, reboot and follow the above steps, then reboot one more time.

Feel free to edit this tutorial or provide feedback on your success or any problems you encounter.

James