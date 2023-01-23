Mon Jan 23 13:38:16 PST 2023
**This should go in the *Issues* tab, and this page should be removed**

    When I open Crosh and try to open Linux from there I am answered with the error message of: "ln: failed to access '/var/run/crouton/mnt/stateful_partition/crouton/chroots/xenial/etc/resolv.conf': Permission denied". I'm not entirely sure what this means except for the fact that I need permission to open this. I am not running these programs on a work or school computer and I am not connected to any enterprise or enterprise like wifi networks. Every other time I have tried to open Linux this way, it works. I am using a Samsung Chromebook, but I am unsure of the model. If anyone knows a way to fix this or what is keeping me from opening Linux, that would be great, thanks 

What it looks like(not a screenshot):
Loading extra module: /usr/share/crosh/dev.d/50-crosh.sh
Welcome to crosh, the Chrome OS developer shell.

If you got here by mistake, don't panic!  Just close this tab and carry on.

Type 'help' for a list of commands.

If you want to customize the look/behavior, you can use the options page.
Load it by using the Ctrl+Shift+P keyboard shortcut.

crosh> shell
chronos@localhost / $ sudo startxfce4
Entering /mnt/stateful_partition/crouton/chroots/xenial...
ln: failed to access '/var/run/crouton/mnt/stateful_partition/crouton/chroots/xenial/etc/resolv.conf': Permission denied
Unmounting /mnt/stateful_partition/crouton/chroots/xenial...
chronos@localhost / $ 
