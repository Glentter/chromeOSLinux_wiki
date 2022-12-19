If you plan to run a server within crouton, you'll need the following:

1. Open a terminal session in your chroot.
2. Install the iptables package, `sudo apt-get install iptables`
3. Add a line to `/etc/rc.local` to launch the service you want. Examples for different services are given below.
4. Add a line to `/etc/rc.local` to open the firewall, for example:
    1. `/sbin/iptables -P INPUT ACCEPT` to accept all inbound traffic. 
    2. `/sbin/ip6tables -P INPUT ACCEPT` to accept all inbound IPv6 traffic. Or,
    3. `/sbin/iptables -I INPUT -p tcp --dport 22 -j ACCEPT` to accept a specific port, e.g. `22` for the SSH example.

This will start the server when the chroot is started, not when ChromeOS is booting. To auto-start the chroot on ChromeOS boot, see [[these instructions|Autostart crouton chroot at ChromeOS startup]].

# OpenSSH Server
Add the following to /etc/rc.local to enable ssh into your chroot. Tested in Ubuntu 13.10. Previously /etc/init.d/ssh start was all that was needed. Works well with [Secure Shell](https://chrome.google.com/webstore/detail/secure-shell/pnhechapfaindjhompbnflcldabbghjo) Chrome app.

On Jessie and Sid only the iptables rule is needed. `sudo system ssh start` will work to start the server.

```
mkdir -p -m0755 /var/run/sshd
/usr/sbin/sshd
```
[update] In Debian 11 Bullseye install the usual ssh stuff via your package manager and open port 22 like this:
```
iptables-legacy -A INPUT -p tcp --dport 22 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
```
Don't use `iptables-nft` because it's broken in Debian 11 or it has troubles running in a chroot. And nowadays we don't know if the command `iptables` is a symlink to `iptables-nft` or `iptables-lecacy` so don't use that either. 

Then start the ssh server daemon like this:
```
sudo service ssh start
```
Check status with `sudo service ssh `.

Commands that use `systemctl` (from systemd) instead of `service` do *not* work in a chroot and therefore do not work in Crouton. 

# avahi and mDNS
1. Install the avahi-daemon, avahi-utils and libnss-mdns packages, `sudo apt-get install avahi-daemon avahi-utils libnss-mdns`
2. In the /etc/avahi/avahi-daemon.conf file, uncomment the `#host-name=foo` record and set the host-name you desire, for example `hostname=mychromebook`.
3. Add the following to /etc/rc.local to enable avahi in your chroot. Tested in Ubuntu 12.04. Do not run avahi-daemon as a daemon (-D option). Doing so will cause it to miss the configuration parameters in /etc/avahi and fail.

`/usr/sbin/avahi-daemon --syslog &`

Optionally, if you are running an OpenSSH or apache server, add corresponding service files to the /etc/avahi/services directory in order to publicize the service on the local network. An example for OpenSSH:
```
cat /etc/avahi/services/ssh.service
<service-group>
  <name replace-wildcards="yes">%h</name>
  <service>
    <type>_ssh._tcp</type>
    <port>22</port>
  </service>
</service-group>
```
A quick test of the service is to ping a known local resource: `ping myhomelanserver.local`. The the local name should resolve to the server IP address.

# LAMP
You'll need to start Apache and MySQL on your rc.local

1. nano /etc/rc.local
2. Paste this using shift+ctrl+v, save with ctrl+x
```
/etc/init.d/apache2 start

export HOME=/etc/mysql
umask 007
[ -d /var/run/mysqld ] || install -m 755 -o mysql -g root -d /var/run/mysqld
/usr/sbin/mysqld &
```
3. save with ctrl+x