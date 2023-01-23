Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
[OpenConnect](http://www.infradead.org/openconnect/) is also a client for Juniper's Junos Pulse SSL VPN.

The required programs to use OpenConnect with a Juniper VPN are
* `ifconfig` to bring the tunnel up/down
* `vpnc` to set the routing and name services up
* `openconnect`
* `juniper-vpn-py` to handle the Junos web interface and optional 2-Factor Auth

Since the Junos Pulse support in OpenConnect is experimental, we will need to build from source.

Step 1: Setup Prerequisites (Including packages for juinper-vpn-py & ifconfig)
````
sudo apt-get install vpnc
sudo chmod a+x+r -R /etc/vpnc
sudo apt-get install libxml2 libxml2-dev gettext make libssl-dev pkg-config libtool autoconf git python-pip net-tools libgnutls-dev 
````
Step 2: Clone and Build OpenConnect
````
git clone git://git.infradead.org/users/dwmw2/openconnect.git
cd openconnect
autoreconf -iv
./configure
make
sudo make install
````
Step 3: Setup juniper-vpn-py
````
sudo pip install mechanize
git clone https://github.com/russdill/juniper-vpn-py
cd juniper-vpn-py
sed -i '/ssl._create_default_https_context = ssl._create_unverified_context/d' ./juniper-vpn.py
sed -i '/ssl._create_default_https_context = ssl._create_unverified_context/d' ./tncc.py
````
Step 4: Add support for resolvconf to /etc/rc.local for automatic DNS update.
See https://github.com/dnschneid/crouton/wiki/VPNC#update-dns
````
mkdir -p /run/resolvconf/interface
cp /etc/resolv.conf /run/resolvconf/resolv.conf
mv /etc/resolv.conf /run/resolvconf/interface/mlan0
ln -s /run/resolvconf/resolv.conf /etc/resolv.conf
resolvconf --enable-updates
````

Okay, you should be ready to rock. If you've encountered errors - please update this wiki with any fixes.

To simplify the VPN connection process you can use a script like the below. Sometimes, the tunnel is not created on the first try. Just escape the script with `Ctrl-C` if there are errors, and rerun it.
````
#!/bin/bash

if (ip tuntap add dev tun0 mode tun)
then
  echo "New tun Created Successfully"
  sleep 4
else
  echo "Old tun Exists, Need to Cleanup"
  ifconfig tun0 down
  ip tuntap del dev tun0 mode tun
  sleep 4
  ip tuntap add dev tun0 mode tun
  sleep 4
fi

if(ifconfig tun0 up)
then
  echo "Interface tun0 is up"
else
  echo "Something went wrong, exiting..."
  exit;
fi

export LD_LIBRARY_PATH="/usr/local/lib"

python /home/louis/VPN/juniper-vpn-py/juniper-vpn.py --host vpn.example.com --user louis --stdin DSID=%DSID% openconnect --juniper %HOST% --cookie-on-stdin --interface=tun0
````

To stop a VPN session, press `Ctrl-C` and kill the interface (sudo ifconfig tun0 down).

*** Update ***

Please follow the suggestions here to keep your tun alive and safe from Shill https://github.com/dnschneid/crouton/issues/2215