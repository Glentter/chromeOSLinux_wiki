Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
An aggravating message when using the target lxde for most releases. Although I haven't found a fix for this, it is apparently related to any release which uses systemd. After clicking OK (or sometimes Esc), the session seems to work OK.

So here is my suggestion: Install lubuntu 14.04.5. Why? Because it does not use systemd! LXDE works beautifully for this release.

EDIT: The below hacks are no longer needed. LXDE works great in Trusty!


***

It will still be necessary to tweak the video settings if you wish to use xorg. Please see:  

[https://github.com/dnschneid/crouton/wiki/Fix-intel-xorg-problems-in-crouton](https://github.com/dnschneid/crouton/wiki/Fix-intel-xorg-problems-in-crouton)


***


So here is the drill:

    sudo sh ~/Downloads/crouton -d -f ~/Downloads/trusty.tar.bz2 -r trusty  
    sudo sh ~/Downloads/crouton -f ~/Downloads/trusty.tar.bz2 -t lxde,xorg,xiwi,keyboard

At least, that is how I do it. LXDE needs a few other tweaks for the best user experience, but I will cover those in another tutorial.