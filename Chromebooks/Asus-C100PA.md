Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
# Usage
The code provided in the main instructions doesn't work with the ASUS Chromebook Flip C100PA.

The instructions provided under [Usage](https://github.com/dnschneid/crouton#usage) are:

`sudo install -Dt /usr/local/bin -m 755 ~/Downloads/crouton`

This code results in the error message:

`install: cannot stat '/root/Downloads/crouton': No such file or directory`

This code worked for me:

`sudo install -Dt /usr/local/bin -m 755 home/chronos/user/Downloads/crouton`