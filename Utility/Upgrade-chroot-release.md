# Ubuntu

## End of Life releases:

crouton nominally doesn't support EOL releases of distros, but feel free to [file a bug](https://github.com/dnschneid/crouton/issues/new) and we'll try to fix things that break.

| Release name | Release # | End-Of-Life date |
| --- | --- | :--- |
| **`precise`** | **12.04 LTS** | **_reached EOL on 28 April 2017_**
| `quantal`  | 12.10 | reached EOL on 16 May 2014
| `raring`  | 13.04 | reached EOL on 27 January 2014
| `saucy`  | 13.10 | reached EOL on 17 July 2014
| `utopic`  | 14.10 | reached EOL on 23 July 2015
| `vivid`   | 15.04 | reached EOL on 04 February 2016

See the [Ubuntu wiki](https://wiki.ubuntu.com/Releases) for a full list.

##### Since `precise` is now EOL'd and `xenial` is now the default release, please upgrade your `precise` chroot(s) [as shown below](#to-upgrade-to-a-more-recent-version-of-ubuntu) or install a new one with `-r xenial`.

##### And it's *always* wise to [backup your chroot(s)](https://github.com/dnschneid/crouton#a-backup-a-day-keeps-the-price-gouging-data-restoration-services-away) before an upgrade, update or any major change.

## To upgrade to a more recent version of Ubuntu:

You can use this graphical method *in* your chroot:

- __[Ubuntu's update manager](http://www.ubuntu.com/download/desktop/upgrade)__

Or follow these instructions in a crosh shell:  

**1.** `sudo enter-chroot -n <chroot_name>`  
    NOTE: (replace `<chroot_name>` with the name of your chroot, e.g. `precise`)

**2.** `sudo apt-get install update-manager-core python-apt`

**3.** `sudo do-release-upgrade`

--------
After the upgrade, *whether it be by the graphical method or the command line*, **_it is important to make sure that crouton is updated_**:

* `sudo crouton -n <chroot_name>`**`-u`**

By default, the name of a chroot is its release name (e.g. a `precise` chroot's name is `precise`).  
After an upgrade, you end up with a `xenial` chroot, whose name is `precise`.  Confusing? You can easily rename it with:

* `sudo edit-chroot precise -m xenial`

--------
### Upgrade from LTS to non-LTS

LTS releases (e.g. `precise`, 12.04) are supported by Ubuntu for 5 years.  
By default, they will not upgrade to non-LTS release (e.g. `quantal` 12.10).  
If you really want to do that, edit `/etc/update-manager/release-upgrades` and replace `Prompt=lts` by `Prompt=normal`.

# Debian

Following the Debian guide and updating the chroot should work. (untested)