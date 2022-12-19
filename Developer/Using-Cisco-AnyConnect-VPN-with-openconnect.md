[OpenConnect](http://www.infradead.org/openconnect/) is a client for Cisco's AnyConnect SSL VPN.

The required programs to use OpenConnect are 
* `openvpn` to create a a persistent tunnel,
* `ifconfig` to bring the tunnel up/down,
* [[VPNC]],
* and `openconnect`

The required calls to connect to a Cisco AnyConnect VPN are
````
VPNGRP=my-vpn-group
VPNUSER=my-vpn-user
VPNURL=https://vpn.mydomain.com

sudo openvpn --mktun --dev tun1 && \
sudo ifconfig tun1 up && \
sudo /usr/sbin/openconnect -s /etc/vpnc/vpnc-script $VPNURL --user=$VPNUSER --authgroup=$VPNGRP --interface=tun1
sudo ifconfig tun1 down
````

Sometimes, the tunnel is not created on the first try. Just escape the script with `Ctrl-C`, if there are errors, and rerun it.

If the VPN connection works sometimes, but not always, execute the commands to create a tunnel and bring it up (sudo openvpn --mktun --dev tun1 && 
sudo ifconfig tun1 up) inside the Chrome shell, and not the chroot. Only the `openconnect` command then should be executed in the chroot. See issue #235 .

To stop a VPN session, also press `Ctrl-C`.

## Cisco AnyConnect ChromeOS App
There is now a Cisco AnyConnect "early adopter preview" ChromeOS app located here:
https://chrome.google.com/webstore/detail/cisco-anyconnect/jacdijibdjifphcecdielmekkmfdpgee

So far it seems to work fine from ChromeOS and still works fine when switching to/from Crouton. This is way easier than the custom scripts I've been using.

## Solution to make the tun0 interface stable and the openvpn command reliable

The tun0 interface is destroyed by the shill service which automatically kills the "useless" connections.  By default this now also includes 'br0' for android apps so we need to keep that in there and add our tun0.
The solution is to enter the following commands before openvpn:

```
sudo stop shill
# On older versions of ChromeOS; check /etc/init/shill.conf
sudo start shill BLACKLISTED_DEVICES="tun0,br0"
# On newer versions
sudo start shill BLOCKED_DEVICES="tun0,br0"
```
the BLACKLISTED_DEVICES tells shill to ignore the "tun0" device (and "br0" for android apps).

To make the life easier it is advised to put a script  under /usr/local/bin/openvpn2

In the following script is included also a trick to change the DNS server on the flight (another known issue, since DNS are not updated by openvpn) :

chronos@localhost /usr/local/bin $ cat openvpn2

```
#!/bin/sh -e
trap '' 2
# Stop shill and restart it with a nicer attitude towards tun0
sudo stop shill
sudo start shill BLACKLISTED_DEVICES="br0,tun0"
# Sleep 10 seconds to allow chromebook to reconnect to the network
sudo sleep 10
sudo openvpn --mktun --dev tun0
sudo sleep 3
# Add google DNS on top of current ones, since openvpn command does not do it
sudo sed -i 's/nameserver/# nameserver/' /var/run/shill/resolv.conf
sudo sed -i '1s/^/# new DNS\nnameserver 8.8.8.8\nnameserver 8.8.4.4\n# old DNS\n/' /var/run/shill/resolv.conf
# Lauch openvpn, finally...
sudo openvpn --config $1 --dev tun0
# When ctrl-c is hit remove tun0 and cleanup the DNS
sudo openvpn --rmtun --dev tun0
sudo sed -i '/# new DNS/,/# old DNS/d' /var/run/shill/resolv.conf
sudo sed -i 's/# nameserver/nameserver/' /var/run/shill/resolv.conf
trap 2
```

finally to open the openvpn connection with a configuration file vpn.ovpn you can give the command:

`openvpn2 vpn.ovpn`

To close the VPN it is enough to hit ctrl-c 

So far the above script has been tested with several openvpn configuration files and it is very reliable.

## Variation on the above solution

The above script wasn't working for me for several reasons:
* My resolv.conf was located at `/var/host/shill/resolv.conf` instead of `/var/run/shill/resolv.conf`
* I use openconnect rather than openvpn, I've never gotten the latter to work for me
* The -e flag in the /bin/sh call was causing trap to not work correctly (maybe a side-effect of using openconnect)

My script is an updated version of pippo0312's script, but splices back in elements of the original script up top, since that originally worked for me. It also can remember your password. Also more whitespace.

```
#!/bin/sh

VPNURL=https://example.com
VPNGRP=GROUPNAME
VPNUSER=username
VPNPASS=password

if [ $USER != "root" ]
then
  echo "Must be run as root."
  exit
fi

# Prevent ctrl-c from killing the whole script
trap '' 2

# Stop shill and restart it with a nicer attitude towards tun0
stop shill
start shill BLACKLISTED_DEVICES="br0,tun0"

# Sleep 10 seconds to allow chromebook to reconnect to the network
sleep 10
openvpn --mktun --dev tun0
sleep 3

# Add google DNS on top of current ones, since openvpn command does not do it
sed -i 's/nameserver/# nameserver/' /var/host/shill/resolv.conf
sed -i '1s/^/# new DNS\nnameserver 8.8.8.8\nnameserver 8.8.4.4\n# old DNS\n/' /var/host/shill/resolv.conf

# Lauch openconnect, finally...
ifconfig tun0 up
echo $VPNPASS | openconnect -s /etc/vpnc/vpnc-script $VPNURL --user=$VPNUSER --authgroup=$VPNGRP --interface=tun0 --no-cert-check --passwd-on-stdin
ifconfig tun0 down

# When ctrl-c is hit remove tun0 and cleanup the DNS
openvpn --rmtun --dev tun0
sed -i '/# new DNS/,/# old DNS/d' /var/host/shill/resolv.conf
sed -i 's/# nameserver/nameserver/' /var/host/shill/resolv.conf

# Stop trapping ctrl-c
trap 2
```

It works flawlessly for me on my Toshiba Chromebook 2, as of 2016-12-14.