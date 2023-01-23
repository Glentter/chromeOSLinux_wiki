Mon Jan 23 13:38:16 PST 2023
Don't try this on ARM
===

After getting Dropbox to compile and run, it complains that it's missing the proprietary daemon, which is impossible to download anywhere for the ARM (new Samsung $249 chromebook). Monitor the feature request [here](https://forums.dropbox.com/topic.php?id=105304). [ This section needs update.]

For x86 and in case the daemon is released
===

Use apt-get if you can:

`sudo apt-get install nautilus-dropbox`

If not, this is a work in progress to help compile dropbox by source. Note the missing packages may differ for your distribution. This is for the default XFCE.

`sudo apt-get install libnautilus-extension-dev python-gtk2 python-docutils`

Download the source and follow instructions at https://www.dropbox.com/install2

`./configure; make`

`sudo make install`

`./dropbox start -i`
