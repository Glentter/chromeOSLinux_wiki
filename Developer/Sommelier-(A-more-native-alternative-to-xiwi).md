Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
# Basic Concepts
`sommelier` works by redirecting X11 and Wayland programs to the built-in ChromeOS wayland server. It even supports hardware acceleration on platforms with "xorg" target support! It was designed for the crostini VM, however early versions of it can be used within crouton with relative ease.
# Set up
### Download
The first step in setting up sommelier is to download it. The version that works the best with crouton currently is the commit [1382ce084cc40790340d672e8b62ec47733cb860](https://chromium.googlesource.com/chromiumos/containers/sommelier/+/1382ce084cc40790340d672e8b62ec47733cb860). You can easily download it as a .tar.gz file [here](https://chromium.googlesource.com/chromiumos/containers/sommelier/+archive/1382ce084cc40790340d672e8b62ec47733cb860.tar.gz).
Then (within your crouton chroot) extract it with:
```sh
cd ~/Downloads/
mkdir sommelier
mv sommelier-1382ce084cc40790340d672e8b62ec47733cb860.tar.gz sommelier
cd sommelier
tar -xzf sommelier-1382ce084cc40790340d672e8b62ec47733cb860.tar.gz
```
### Installation
After downloading sommelier, the first thing you should do is setup its environment variables. These will need to be set whenever it's run, so you probably should add them to `~/.bashrc`.
```sh
export GDK_BACKEND=wayland 
export CLUTTER_BACKEND=wayland
export XDG_RUNTIME_DIR='/var/run/chrome'
export WAYLAND_DISPLAY=wayland-0
export DISPLAY=:0
```
Next, you'll need to build it. Thankfully, this is relatively simple. First, install the necessary dependencies.
```sh
sudo apt update
sudo apt install -y pkg-config git make xwayland libwayland-dev libgbm-dev gcc libx11-xcb-dev libsystemd-dev libxcb-composite0-dev libxkbcommon-dev libxrender-dev libxtst-dev libpixman-1-dev
```
Then actually build it!  Whilst in sommelier's directory:
```sh
make
```
Then, in order to run sommelier as shown below, make the `sommelierrc` file executable:
```sh
chmod +x sommelierrc
```
You can also install it:
```sh
sudo make install
```

Finally, to stop getting a warning when running sommelier (`_XSERVTransmkdir: Owner of /tmp/.X11-unix should be set to root`), change the owner of `/tmp/.X11-unix` to root:
```sh
sudo chown root:root /tmp/.X11-unix
```
### Running sommelier
For simple forwarding (no hardware acceleration), simply execute this command in sommelier's directory:
```sh
./sommelier -X --x-display=:0 --no-exit-with-child /bin/sh -c "./sommelierrc"
```


It might be helpful to add that command to a bash file to run it quickly - for example, add the following line to the end of your `~/.bashrc` file (along with the exports listed above):
```sh
alias sommelier='~/Downloads/sommelier/sommelier -X --x-display=:0 --no-exit-with-child /bin/sh -c "~/Downloads/sommelier/sommelierrc"'
```

### Once again, with hardware acceleration
Run this monster of a command for forwarding with hardware acceleration:
```sh
./sommelier -X --x-display=:0 --glamor --drm-device=/dev/dri/renderD128 --virtwl-device=/dev/null --shm-driver=noop --data-driver=noop --display=wayland-0 --peer-cmd-prefix=/lib64/ld-linux-x86-64.so.2 --xwayland-gl-driver-path=/usr/lib64/dri --xwayland-path=/usr/bin/Xwayland /bin/sh -c "./sommelierrc"
```
To run sommelier without sommelierrc:
```sh
sommelier -X --x-display=:0 --glamor --drm-device=/dev/dri/renderD128 --virtwl-device=/dev/null --shm-driver=noop --data-driver=noop --display=wayland-0 --peer-cmd-prefix=/lib64/ld-linux-x86-64.so.2 --xwayland-gl-driver-path=/usr/lib64/dri --xwayland-path=/usr/bin/Xwayland </path/to/binary>
```
It's recommended you put an alias in your `~/.bashrc` for quick execution:
```sh
alias sommelier="sommelier -X --x-display=:0 --glamor --drm-device=/dev/dri/renderD128 --virtwl-device=/dev/null --shm-driver=noop --data-driver=noop --display=wayland-0 --peer-cmd-prefix=/lib64/ld-linux-x86-64.so.2 --xwayland-gl-driver-path=/usr/lib64/dri --xwayland-path=/usr/bin/Xwayland"
```
Voilà! With that in your `~/.bashrc`, you can now do `sommelier <program>` and it will forward to ChromeOS with hardware acceleration.

## Some scripts

You can install the following script in `/usr/local/bin/runsommelier` *inside* the chroot to start `sommelier` and an application under it. Further applications can be started the same way with `runsommelier name_of_application`.
```
#!/bin/bash
# Copyright (c) 2016 The crouton Authors. All rights reserved.
# Use of this source code is governed by a BSD-style license that can be
# found in the LICENSE file.

export GDK_BACKEND=x11
export CLUTTER_BACKEND=wayland
export XDG_RUNTIME_DIR=/var/run/chrome
export WAYLAND_DISPLAY=wayland-0
export DISPLAY=:0
export SCALE=1
SOMMELIER_ACCELERATORS="Super_L,<Control>space,<Alt>space,<Alt>bracketleft,<Alt>bracketright,<Alt>minus,<Alt>equal"

SOMM=$(pidof sommelier 2> /dev/null)
if [ -z "$SOMM" ]; then
  sommelier -X --x-display=$DISPLAY --scale=$SCALE \
  --accelerators=${SOMMELIER_ACCELERATORS} --no-exit-with-child \
  /bin/sh -c "/usr/local/etc/sommelierrc" > /tmp/sommelier.log 2>&1 &
#  /bin/sh -c "/usr/local/etc/sommelierrc" &
  sleep 3
fi

SOMM=$(pidof sommelier 2> /dev/null)
if [ ! -z "$SOMM" ]; then
  echo "sommelier process $SOMM is running"
else
  echo "sommelier failed to start"
  exit 1
fi

if [ "$1" ]; then
#    exec sommelier "$@" &
    exec sommelier "$@" >> /tmp/sommelier.log 2>&1
fi
```
Next, you can install the following script in `/usr/local/bin/startsommelier` under ChromeOS to replace `startxiwi` with `startsommelier`. So `startsommelier -b lxterminal` will pop-up a terminal.
```
#!/bin/sh -e
# Copyright (c) 2016 The crouton Authors. All rights reserved.
# Use of this source code is governed by a BSD-style license that can be
# found in the LICENSE file.

set -e

APPLICATION="${0##*/}"
ENTERCHROOT="$(dirname "$(readlink -f -- "$0")")/enter-chroot"
OPTS_ENTER=''
OPTS_SOMM=''

USAGE="$APPLICATION [options] chroot_app [parameters]

Wraps enter-chroot to launch a window in Chromium OS for any graphical application.
Applications launched in this way show as independent windows.

By default, it will use the primary user on the first sommelier-enabled chroot found and launch
the chroot_app in a window.

Options:
$("$ENTERCHROOT" -h 2>&1 | grep -e ' -[bckntu]')

You should be able to use normal keys inside the application. To use
ChromeOS keys you may need to hit Search first.
"

while getopts 'bc:k:n:t:u:FTf' OPT; do
    case "$OPT" in
      b) OPTS_ENTER="$OPTS_ENTER -$OPT";;
      c|k|n|t|u)
         OPTARG="$(echo -n "$OPTARG" | sed -e "s/'/'\\\\\\''/g")"
         OPTS_ENTER="$OPTS_ENTER -$OPT '$OPTARG'";;
      f) OPTS_SOMM="$OPTS_SOMM -$OPT";;
      \?) echo "$USAGE" 1>&2
          exit 2;;
    esac
done
shift "$((OPTIND-1))"

if [ "$#" = "0" ]; then 
    echo "$USAGE" 1>&2
    exit 2
fi

#eval "exec sh -e \"\$ENTERCHROOT\" -t xiwi $OPTS_ENTER \
#    exec xiwi $OPTS_SOMM \"\$@\""
eval "exec sh -e \"\$ENTERCHROOT\" $OPTS_ENTER \
    exec runsommelier \"\$@\""
```

# You're done!
Now you can use sommelier to run most graphical programs!

Whilst you have sommelier running, run a program (probably from another ChromeOS terminal, having entered your crouton chroot again with something like `sudo enter-chroot`).

If you want to be clever, and have set up the alias above, you can run sommelier in the background with `sommelier &` and then run your graphical program(s) from the same terminal / chroot - sommelier will stop when you exit the chroot.

Note: Programs running through sommelier don't appear to get an icon in the ChromeOS shelf for any of their windows, and you may run into issues if a program goes fullscreen.

![](https://github.com/jcdang/chromeos-ubuntu-sommelier/raw/master/intellij.png)
# Special thanks
Special thanks to jcdang for detailing the steps required to running sommelier at https://github.com/jcdang/chromeos-ubuntu-sommelier.