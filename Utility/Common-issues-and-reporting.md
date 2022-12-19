Most issues can be fixed by downloading a fresh installer, and updating your chroot:

 1. Remove any existing installer from your `Downloads` folder.
 2. Fetch a fresh installer: [https://goo.gl/fd3zc](https://goo.gl/fd3zc)
 3. Open a crosh shell (Ctrl-Alt-T, then type `shell`)
 4. Prep the installer with: `sudo install -Dt /usr/local/bin -m 755 ~/Downloads/crouton` 
 5. Figure out the name of your chroot(s). If you do not know, `sudo edit-chroot -a` (on recent enough crouton), or `sudo ls /usr/local/chroots` will provide you with a list of chroot names.
 6. Run an update: `sudo crouton -n chrootname -u`, where `chrootname` is the name of your chroot, 
make sure there are no errors.
 7. Reboot: shutdown completely and restart.
 8. Try starting your chroot again.

If the problem still occurs:
 - Check [existing issues](https://github.com/dnschneid/crouton/issues?q=is%3Aopen+is%3Aissue) if your problem has already been reported. If you are not 100% sure this is the same issue, open a new one, it's easier for the devs to mark an issue as duplicate than keeping track of 2 issues under the same thread.
 - Open a new issue. Be sure to include: 
   - The exact command you run
   - At least the last 20 lines of output
   - Any error in the update process above
   - The output of `sudo enter-chroot -n chrootname croutonversion`, where `chrootname` is the name of your chroot (see above).