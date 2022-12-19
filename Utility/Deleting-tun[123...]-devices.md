CTL-ALT-T to open a crosh developer shell, enter the shell

crosh> shell

To see the list of network adapters:

% ifconfig -a

After my toying with my VPN settings, I had 4 extra tun devices, and it didn't seem as if chromeos has a cleanup script.

To delete the tunX devices, repeat for as many tun[?]:

%  sudo ip tuntap del dev tun1 mode tun

If you accidentally delete tun0, connecting with chromeos VPN will restore it.