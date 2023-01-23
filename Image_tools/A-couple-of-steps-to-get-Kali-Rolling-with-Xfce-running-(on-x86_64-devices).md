Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
1. `sudo crouton -r kali-rolling -t audio,core`

2. `sudo enter-chroot -n kali-rolling`

       a. sudo apt install curl

       b. sudo curl -O "http://http.us.debian.org/debian/pool/main/x/xbindkeys/xbindkeys_1.8.6-1_amd64.deb"

       c. sudo apt install ./xbindkeys*

       d. exit

3. `sudo crouton -r kali-rolling -t xfce -u`