Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
The steps in the wiki were tested on a Samsung Chromebook Plus with [crouton integration](https://chrome.google.com/webstore/detail/crouton-integration/gcpneefbbnfalgjniomfjknbcgkbijom).

#### Installing LyX
At time of writing, the LyX version offered in the `xenial` repositories was 2.1.x. Since 2.2 was the first version to support HiDPI it is recommended to install a distro whose repositories offer a newer version, for example `stretch`. Therefore, install your chroot with

    sudo sh ~/Downloads/crouton -r stretch -t xiwi
After the installation is complete, enter your chroot and install LyX

    sudo enter-chroot
    sudo apt install lyx
#### Enabling HiDPI and scaling
Still in chroot, edit `/etc/crouton/xserverrc-xiwi` and add `-dpi 200x200` before the right `"` in the line that starts with `XARGS`. This can be done with [vi](https://en.wikipedia.org/wiki/Vi) or any other text editor. The value `200x200` can be replaced with any value of your liking.
When you find a value you are satisfied with, create a backup of `/etc/crouton/xserverrc-xiwi` as this file will be reset when you update your chroot (see the [source](Chromebook-Pixel#hidpi-in-xiwi-window) of this workaround):

    sudo cp /etc/crouton/xserverrc-xiwi /etc/crouton/xserverrc-xiwi.bak
Leave the chroot with `Ctrl+D` and launch LyX<sup>1</sup>

    sudo startxiwi lyx
In a Chrome browser window, click on the crouton extension icon and check `HiDPI` (this requires [crouton integration](https://chrome.google.com/webstore/detail/crouton-integration/gcpneefbbnfalgjniomfjknbcgkbijom)).
In LyX, rightclick any icon to open the icon-size menu and choose a more reasonable icon size (this step doesn't work in LyX 2.1.x).

<sup>1</sup> At time of writing, there is an [issue](https://github.com/dnschneid/crouton/issues/3474) with launching X on stretch. Luckily, there is also a workaround (in the issue page).

#### Starting LyX
To start LyX, run

    sudo startxiwi lyx