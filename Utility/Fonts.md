# Install (Powerline) fonts in Chrome OS in dev mode

Suppose you want to install some extra fonts (eg [Powerline fonts](https://github.com/powerline/fonts)) to spice up your Crosh Window (eg for [Zsh + agnoster](https://github.com/agnoster/agnoster-zsh-theme)). You might be surprised that after following a tutorial (even those specific to Chrome OS) that places the fonts in your home directory  `fc-cache -vf` does not pick up your new fonts despite the existence of `/etc/fonts/conf.d/50-user.conf` (and even if you manually specify the directory, you will still run into problems with the powerline symbols).
 
The reason is that [the Chrome OS devs have patched the fontconfig to disable loading files from the home directory](https://groups.google.com/a/chromium.org/forum/#!msg/chromium-os-dev/SOD7a16iZ7U/mAC1c63MVEwJ).

So how can you install fonts? The solution is to install to `/usr/local/share/fonts`. This location is not in the default fontconfig config so you need to add

```
<dir>/usr/local/share/fonts</dir>
```
to `/etc/fonts/local.conf`

If it complains about being readonly you should remount the partition in rw mode: https://github.com/dnschneid/crouton/issues/582#issuecomment-32581668

Unfortunately `/etc/fonts/local.conf` will be reset on update, so alternatives are very welcome!