# kickoff
To get this working, we will use three things:
Passwordless SSH (and ssh server), Crostini, and Crouton.  
# Crouton setup  
Let's be admin, so `sudo` isn't nagging:
```
sudo su
```

First, port 80 needs to be open, install iptables to do so:
```
apt-get install iptables -y
```

To open the port every launch, use:
```
echo '/sbin/iptables -I INPUT -p tcp --dport 22 -j ACCEPT' >> /etc/rc.local
```

what does port 22 to do with 80?
nothing