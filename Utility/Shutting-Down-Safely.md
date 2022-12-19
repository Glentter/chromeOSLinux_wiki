Although shutting down ChromeOS with an active chroot may work, it isn't recommended. Chroots on SD cards are particularly susceptible to corruption.

To cleanly umount the chroot, first logoff/exit any graphical environment you are running (how to do this depends on the graphical environment, although switching back to the Crouton tab and hitting <kbd>CTRL-C</kbd> should normally work if your chroot is running in the foreground), then type `exit`. You should see something like this.

    Unmounting /mnt/stateful_partition/crouton/chroots/jessie...
    Sending SIGTERM to processes under /mnt/stateful_partition/crouton/chroots/jessie...
    chronos@localhost /usr/local/bin $ 

It is safe to shut down ChromeOS at this point.