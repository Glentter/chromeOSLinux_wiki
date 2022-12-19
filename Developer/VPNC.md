# VPNC

Chromium OS has VPN support built-in, but connecting to some VPNs requires client features and configurations that are not available. For those networks, it is possible to establish a VPN connection from a chroot that is usable from both within the chroot and Chromium OS.

These instructions assume you are running an Ubuntu chroot and have been tested with precise.

### Installation

`$ sudo apt-get install vpnc`

### Configuration

Create a `vpnc` conf file as per `man vpnc`. A selection from the man page to help get you started:

<pre>This is an example vpnc.conf with pre-shared keys:

          IPSec gateway vpn.example.com
          IPSec ID ExampleVpnPSK
          IKE Authmode psk
          IPSec secret PskS3cret!
          Xauth username user@example.com
          Xauth password USecr3t

And another one with hybrid  authentication  (requires  that  vpnc  was
built with openssl support):

          IPSec gateway vpn.example.com
          IPSec ID ExampleVpnHybrid
          IKE Authmode hybrid
          CA-Dir /etc/vpnc
          or
          CA-File /etc/vpnc/vpn-example-com.pem
          IPSec secret HybS3cret?
          Xauth username user@example.com
          Xauth password 123456
</pre>

### Connect to the VPN

#### Using TAP

`$ sudo vpnc --ifmode tap ~/vpn/myvpn.conf`

#### Using TUN

The tun0 interface is destroyed by the shill service which automatically kills the "useless" connections. The solution is to enter the following commands before openvpn:

```
sudo stop shill
sudo start shill BLACKLISTED_DEVICES=tun0
sudo vpnc --ifmode tun ~/vpn/myvpn.conf
```

the BLACKLISTED_DEVICES tells shill to ignore the "tun0" device.


Given a valid `~/vpn/myvpn.conf`, this will create a VPN tunnel that is shared by both the chroot and Chromium OS.

### Update DNS

> **Note:** You can skip this section if you're using vpnc 0.5.3r512-2 under Debian Jessie because that _automatically_ updates your DNS as you would expect.  See [issue #796](https://github.com/dnschneid/crouton/issues/796) for more information.

The VPNC options for automatically updating DNS don't seem to work, so until someone finds an automatic solution for re-configuring DNS you'll have to do it manually. This can be accomplished by overwriting `/var/host/shill/resolv.conf` with a hard-coded version configured for your VPN. This can be automated by writing a simple shell script around the vpnc connect/disconnect. To preserve continuity of connectivity across VPN connects/disconnects, I recommend stashing away a copy of `resolv.conf` before overwriting during connect, and then restoring the stashed copy during disconnect.

As with the VPN connection itself, `/var/host/shill/resolv.conf` is shared between the chroot and Chromium OS, so your DNS changes will take effect in both environments.

[Here is an example bash script to connect to a cisco vpn, including backing up and overwriting dns on connect, and restoring dns on disconnect.](https://github.com/RaymiiOrg/df/blob/master/vpnc.sh)

Alternatively, in /etc/rc.local:

```
mkdir -p /run/resolvconf/interface
cp /etc/resolv.conf /run/resolvconf/resolv.conf
mv /etc/resolv.conf /run/resolvconf/interface/mlan0
ln -s /run/resolvconf/resolv.conf /etc/resolv.conf
resolvconf --enable-updates
```

and then vpnc can update /etc/resolv.conf as the vpn goes up and down.


### Disconnect from the VPN

This is supposed to work:

`$ sudo vpnc-disconnect`

However, you may find that the vpn `tap` doesn't always want to go down gracefully, in which case you can try a more aggressive approach:

`$ sudo pkill -9 vpnc`

### Optionally Disable Wifi Power Management

Chromium OS enables wifi power management by default, which will cause your wifi radio to turn off somewhat aggressively during short idle periods. This can be very inconvenient when using vpnc since there is no auto-reconnect, and especially if your particular VPN requires a token passcode on each connect. You may find it useful to disable wifi power manager while connected to the VPN to help stay connected. This can be accomplished via the `wireless-tools` package:

`$ sudo apt-get install wireless-tools`

Once installed, you can enable or disable wifi power management in your vpnc connect/disconnect scripts as follows:

<pre>$ sudo iwconfig wlan0 power off
$ sudo iwconfig wlan0 power on</pre>

### Optionally Disable Chromium OS Power Management

The same disclaimers apply if Chromium OS decides to put your Chromebook into sleep. You may want to look into the various options available to disable Chromium OS power management, such as [Caffeine](https://groups.google.com/forum/#!topic/chromebook-central/QORv0mDLts0), or using `croutonpowerd` as follows:

Disable Chromium OS power management:<br>
`$ croutonpowerd -i &`

Enable Chromium OS power management (avoid killing the --daemon instance that was started by crouton):<br>
`$ pkill -f croutonpowerd\ -i`

##See also

* [[Using Cisco AnyConnect VPN with openconnect]]

If you want to use directly a ".ovpn" config file from the shell you can use the method described below:

* [[Using-Cisco-AnyConnect-VPN-with-openconnect#solution-to-make-the-tun0-interface-stable-and-the-openvpn-command-reliable]]