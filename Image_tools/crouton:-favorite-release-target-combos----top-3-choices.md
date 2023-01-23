Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
Theoretically, you can install any target with any release. But not all combinations work equally well. Here is what I've discovered:

1. -r trusty -t lxde
2. -r xenial -t xfce
3. -r stretch -t gnome

You can add the extra goodies to each of the above. For example:

`sudo crouton -r trusty -t lxde,xorg,xiwi,keyboard`

lxde is my favorite DE (Desktop Environment), but it's very bare bones, and needs some manual tweaking in order to work well. The lxde target, in my experience, only works properly in trusty; in other releases, you get the irritating "no session for pid" error.

James