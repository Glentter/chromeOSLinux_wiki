Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
## Printing from Crouton

You want to print from your crouton? 

1. Install cups and related packages:
`sudo apt-get install cups system-config-printer-gnome`
<br/>To get a working `lpr` command, also install `cups-bsd`. You'll also want to install `hplip` if you're using an HP printer. Get the latest hplip package and make from [http://hplipopensource.com/](http://hplipopensource.com/). There maybe other packages necessary for other printers, please add them here.
<br/>If you want only browser-based cups admin, leave out `system-config-printer-gnome`.

2. Add yourself to the lpadmin group:
`sudo adduser <username> lpadmin`

3. init scripts don't work right in crouton so we need to start cups somehow. One way is to edit `/etc/rc.local` and add:
`/usr/sbin/cupsd`

4. If you want to connect to remote CUPS servers, install `cups-browsed`, put your configuration into `/etc/cups/cups-browsed.conf`, and in `/etc/rc.local` add:
`/usr/sbin/cups-browsed &`

5. Log out of your crouton and back in. 

6. Add or configure printers using either system-config-printer-gnome, or using the CUPS web interface -- open a browser window and type [http://localhost:631](http://localhost:631). When managing printer, if you can't get pass the authentication, check that you have add the user to lpadmin group as described previously, and check that only one /usr/sbin/cupsd is running at the same time.  

_This has been tested on a shared network printer.  It also works for directly connected printers, including an ML-1740._


***


Samsung ARM Chromebook and HP printers requiring proprietary plugin, e.g. HP p1102w

The proprietary plugin from HP for certain printers is a binary file downloaded as part of the hplip installation process. The source code is not available and the binary will not run on ARM processors. This is a known limitation and is well documented at [https://bugs.launchpad.net/hplip/+bug/1152219](https://bugs.launchpad.net/hplip/+bug/1152219)

** UPDATE ** Plugin files for ARM are now supported in hplip 3.15.4

** Changed in: hplip
       Status: In Progress => Fix Released

You received this bug notification because you are subscribed to the bug
report.

An alternative printer driver is available from [http://foo2zjs.rkkda.com/](http://foo2zjs.rkkda.com/)

## Printing from Chrome OS

The instruction above allows you to print from your crouton. But what if you want to print from your Chrome OS? By default Chrome OS allows printing only through Google Cloud Print, which is fine if you have a Google Cloud Print compatible printers, or a Windows or Mac machine to host the classic (non Google Cloud compatible) printers. Chance are if you reach this page, you don't. 

If you have HP printers try installing [HP Print for Chrome](https://chrome.google.com/webstore/detail/hp-print-for-chrome/cjanmonomjogheabiocdamfpknlpdehm?hl=en-US) which should allows you to print to classic HP printers.  

### CUPS printer connector for Chrome/Chromebooks

If you have already set up CUPS in your chroot as per the instructions above or have a CUPS print server on the local network you can try the [CUPS / IPP extension for Chrome](https://chrome.google.com/webstore/detail/ipp-cups-printing-for-chr/lkhfeoafdgbaecajkdbioenncjopbpmk) which allows you to print to multiple CUPS connected printers without going through Cloud Print. The CUPS/IPP print option option will appear in the printer list of Chrome(OS). Remember to configure the extension with your full printer URI: something like https://localhost:631/printers/my_printer - check the CUPS extension help section for details.

### Connecting classic printers to Google Cloud Print

If you have some other brands or the above extension didn't work, here are two other ways you can print from Chrome to classic printers:

1. [Google CUPS connector](https://github.com/google/cups-connector). The official way provided by Google to link Google Cloud Print account to CUPS. That way, all your local printers listed in CUPS will be made available in your Google Cloud Print and you can select those printers when printing through Chrome. 
2. Python [cloudprint](https://github.com/armooo/cloudprint). Unofficial script that allows you get the same result, easier to setup, less advanced functionalities (e.g. can't automatically share the printers to other google accounts -- relevant in organizations).

Note that both assumed you already setup printer(s) in CUPS at localhost on previous instructions and can successfully do a print job, and set CUPS to run automatically in `/etc/rc.local`. If you haven't done this please go back to the beginning of this page and run them through. 

### Google CUPS connector

Follow the installation setup at https://github.com/google/cups-connector, or here is a tested instruction for ARM-based Chromebooks (tried in trusty) that you can follow fairly quickly.

Compile Go v1.4+. Google CUPS connector was written in Go and requires at least Go v1.4. Ubuntu repository only has binary up to v1.2, so you need to compile Go 1.4:

    sudo apt-get install git curl binutils bison gcc make
    bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
    source /home/$USER/.gvm/scripts/gvm
    gvm install go1.4
    gvm use go1.4 --default

Compile Google CUPS Connector codes

    sudo apt-get install build-essential libcups2-dev libsnmp-dev
    go get github.com/google/cups-connector/connector
    go get github.com/google/cups-connector/connector-init
    go get github.com/google/cups-connector/connector-monitor
    go get github.com/google/cups-connector/connector-util

Generate configuration file
    
    cd ~
    connector-init -retain-user-oauth-token="false" -socket-filename="/tmp/cups-connector-monitor.sock" -proxy-name="Crouton-Proxy-1"

Open the URL on chrome and press accept to give Google CUPS Connector access to your Google Cloud Print account. Copy the resulting code, paste to crouton shell, then press enter.

Test the service

    connector

Open https://www.google.com/cloudprint#printers check that your CUPS printer(s) are listed. Done!

To run the Google Cloud Connector again in the future, enter-chroot and run `connector` from your home directory.

### Python cloudprint

Install [cloudprint](https://github.com/armooo/cloudprint)

    sudo apt-get install python-pip python-dev
    sudo pip install cloudprint[daemon]

If your Python is less than v2.7.9 (most likely), update the SSL library:

    sudo apt-get install libffi-dev libssl-dev
    sudo pip install pyopenssl ndg-httpsclient pyasn1

Generate configuration file

    cloudprint

Open the short URL on Chrome and press Finish printer registration, click manage printer, check that your printer(s) are listed. Done!

To run the cloudprint again in the future, enter-chroot and run `cloudprint -d` from your home directory.

 