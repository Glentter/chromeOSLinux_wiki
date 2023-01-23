Mon Jan 23 13:38:16 PST 2023
If you like Atom (the editor) it is easy to run it on a Chromebook. It runs both in a graphic environment and in a browser tab. I tested on an HP Chromebook 13 and on an Asus Chromebox M085U.

# Installation
The installation progresses as usual. There is a source for installing on ubuntu trusty here: [https://codeforgeek.com/2014/09/install-atom-editor-ubuntu-14-04/](https://codeforgeek.com/2014/09/install-atom-editor-ubuntu-14-04/). Alternatively you can download the .deb file from Atom.io and install with `sudo dpkg -i atom-amd64.deb`, or whatever name the downloaded package has, from the command prompt after you have entered the chroot.

You can now open atom from within your graphical interface if installed, but it is a little more tricky to start it with xiwi. Atom, when starting from the command line, expects to be able to read the directory and apparently has more than one mode. 

# Run in a browser tab
So to start Atom in a browser window, go to the directory that you want to edit (that means you have the rights to do that as well) and then issue the command:

    xiwi -T atom -f .

from your chroot. The -f option signals to atom to run in the foreground, the . (dot) tells atom to open the current directory for editing. Without the `-f .` it may start or it may fail with an error (-2). In any case, the browser window will  not close when closing atom without the -f option.