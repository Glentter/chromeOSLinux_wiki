Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
### Use ssh -R to access your VNC Server

Setting up a VNC Server inside your crouton works fine, however Chrome OS seems to block port 5900.

You can try crouton-vnc but if you want just a quick way to connect into your crouton (you need to install (or have already) an ssh-server on the client machine) then you can use the -R command of ssh to make a reverse connection:
(this is for Ubuntu but the general idea of using ssh should work on other distros/croutons as well)

1. Install and run `vino-preferences` to start a VNC server listening on port 5900.

2. If you're using Ubuntu 14.04 don't forget the [Vino encryption bug](https://bugs.launchpad.net/ubuntu/+source/vino/+bug/1281250) workaround:
`dconf write /org/gnome/desktop/remote-access/require-encryption false`

3. On your client PC make sure you have an ssh server running.

4. Inside your crouton use ssh to connect to the client machine, i.e. ssh from the host to the client, and use -R to forward port 5900:
`ssh -R 5900:localhost:5900 clientip`

5. On your client PC, in vncviewer specify 127.0.0.1 as the VNC Server address. Voila.
