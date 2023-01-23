Mon Jan 23 13:38:16 PST 2023
# What is Debian?
Debian is a common Linux distribution that uses the APT package manager, dpkg, and .deb. The popular Linux distribution Ubuntu is based on Debian, Hence why it uses the same package manager. Debian has a large repository of packages and it is the main selling point. its website is [debian.org](https://www.debian.org/)
# Should I use it?
Sure, but it doesn't include many useful packages by default though so you need to manually install things like LibreOffice, Firefox, and for the Gnome3 shell, you don't get a taskbar by default. (to fix this, get a Gnome shell extension like dashtodock.
# How do I install it?
`cd`

`sudo install -Dt /usr/local/bin -m 755 ~/Downloads/crouton`

`sudo crouton -r <available Debian distribution>`

for a DE other than xfce4, use `-f [available DE]`