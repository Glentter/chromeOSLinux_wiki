**source of information:** https://www.chromium.org/chromium-os/packages/power_manager

**powerd:** user-space daemon running as the "power" user that initiates dimming the screen, suspending the system, etc.

**how it works:** powerd's default preferences are stored on the _read-only_ partition in `/usr/share/power_manager`. These preferences _can be overridden_ by identically-named files on the stateful partition in `/var/lib/power_manager`. In most cases, preference changes won't take effect until the powerd process is restarted.

A script to shorten the screen timeout is already pre installed at `/usr/bin/set_short_powerd_timeouts`. Open it up to see how it's done, or see it in action by executing it with `sudo set_short_powerd_timeouts`. (add `--reset` to undo)

Example to change the 'lid close action' to do nothing, open a crosh shell and follow these commands:
```
sudo -u power cp /usr/share/power_manager/use_lid /var/lib/power_manager       #copy default config
sudo su                                                                        #cant sudo next command
echo 0 >/var/lib/power_manager/use_lid                                         #change the action to do nothing
restart powerd                                                                 #let the change take effect
exit                                                                           #exit root 
```


To disable idle suspend, open a crosh shell and follow these commands:
```
sudo su                                                                        #cant sudo next command
echo 1 >/var/lib/power_manager/disable_idle_suspend                            #change the action to do nothing
chown power:power /var/lib/power_manager/disable_idle_suspend                  #it belongs to power
restart powerd                                                                 #let the change take effect
exit                                                                           #exit root 
```


A wrapper script to disable lid and idle suspend while your chroot is active:
```
#!/bin/sh -e

ROOTUID="0"

if [ "$(id -u)" -ne "$ROOTUID" ] ; then
  echo "This script must be executed with root privileges."
  exit 1
else
  echo 1 >/var/lib/power_manager/disable_idle_suspend
  echo 0 >/var/lib/power_manager/use_lid
  ( (status powerd | fgrep -q "start/running" ) && restart powerd ) || \
  start powerd
  echo "Disabled idle/lid suspend"
  
  #change this with the command you start your chroot with
  startunity $@ || true # ignore any failures here so fix executes

  rm -f /var/lib/power_manager/disable_idle_suspend
  rm -f /var/lib/power_manager/use_lid
  ( (status powerd | fgrep -q "start/running" ) && restart powerd ) || \
  start powerd
  echo "Enabled idle/lid suspend"
fi
```