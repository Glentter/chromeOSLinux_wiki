> Some steps are from George Smart's blog and SANE website.

1.  install packages:  
sudo apt-get install xsane sane libsane-extras sane-utils netpbm libusb
2.  download the firmware:'Esfw41.bin'
3.  copy firmware:  
sudo cp -v Esfw41.bin /lib/firmware/esfw41.bin 
4.  modify the config file: add 'firmware /lib/firmware/esfw41.bin' to '/etc/sane.d/snapscan.conf' 
5.  find the scanner:   
lsusb   
sane-find-scanner
6.  find the device:   
scanimage -L   
In my case(Epson Perfection 2480), it's 'snapscan:libusb:001:005'
7.  test it:   
sudo scanimage -d snapscan:libusb:001:005 --format pnm > out.pnm
8.  convert it to jpg:   
pnmtojpeg out.pnm > out.jpg   
or   
sudo scanimage -d snapscan:libusb:001:005 --mode color -x 215 -y 285 --resolution 600 |pnmtojpeg >out.jpg
9.  done.    

> Epson package(iscan) looks not a must, neither does X. maybe xsane either