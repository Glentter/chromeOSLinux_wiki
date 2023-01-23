Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
# Basic concepts
`xiwi` (X11 in a Window) works by creating a window in Chrome and channeling a virtual X11 framebuffer over to it.  While crouton transfers the framebuffer relatively efficiently, there's no GPU acceleration and a fair bit of overhead, so don't expect games or graphics-heavy desktop environments to perform well.  For basic productivity use it works great, though.

Regardless of how you intend to use xiwi (full desktop environment vs. single-app), you need to add it to your chroot.

1. Install the `xiwi` target into your chroot, either via update (`crouton -u -t xiwi`) or via initial install (`crouton -t xiwi,xfce`).  If you only intend to use the single-app mode, you can avoid installing a desktop environment entirely (`-t xiwi`).
2. Ensure you have the [Chromium OS 'crouton integration' extension](https://chrome.google.com/webstore/detail/crouton-integration/gcpneefbbnfalgjniomfjknbcgkbijom) installed.

If you want to temporarily launch using native Xorg, add the -X parameter to your enter- or start* command (e.g.: `sudo startxfce4 -X xorg`).

You can set the default xmethod back to xorg by updating with the `xorg` target explicitly, after which you can still use xiwi via -X (e.g.: `sudo startxfce4 -X xiwi`).

Hit the full screen (above the 5 key) or switch window (above the 6 key) to get out of full-screen crouton.

If you close the window, xiwi is still running.  You can get back to your windows via the 'crouton integration' extension dropdown.

<kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Shift</kbd>+<kbd>Back/Forward</kbd> still works to cycle chroots, although it makes less sense here since Chromium OS's window management works fine too.

You can have xiwi chroots and xorg choots running simultaneously.  Usually it's not a good idea to run two instances of a desktop environment in the same chroot at the same time, but in most cases it's fine to run multiple xiwi single-application instances even alongside a desktop environment inside the same chroot.

# Running full desktops in a window
Once you have xiwi set up, launching your desktop is the same as if you were using xorg (i.e., `sudo startxfce4`).  

# Running full desktops in a tab
Example: `sudo startxfce4 -n chrootname -X xiwi-tab`
Substitute any graphical desktop start script as desired. You can toggle to full screen and back to tab via F4. You can close the tab via the 'X' on the tab itself and then reopen the tabbed session via the crouton extension pulldown menu. This tip will save you endless hassles of stopping and restarting a chroot from the commandline each time, you can just close it and reopen it from the plugin pulldown as often as necessary. If you logout of the session while in fullscreen, the shell command exits and you have to launch from the crosh prompt again.

# Running individual applications in windows and tabs
With xiwi installed, you do not need a desktop environment.  You can launch apps on an individual basis in a Chromium OS window or tab.

1. Enter your chroot to get a command line: `sudo enter-chroot`
2. Launch your app via the `xiwi` command: `xiwi gimp`
3. Use `xiwi -T gimp` to launch in a tab, or `xiwi -F gimp` to launch full-screen.
4. You can combine those into a single command `sudo enter-chroot xiwi -T gimp`.
5. You can silence output and run the command in the background via `sudo enter-chroot -b xiwi -T gimp`.

If your application forks and quits, xiwi may get confused and quit as well (you'll get an error/quit when the window launches).  Search your application's documentation for a parameter that prevents forking (oftentimes it's `-f`).  If you can't find one, you can pass `-f` to xiwi itself (e.g., `xiwi -f gvim`), and xiwi will not close unless there are no applications visible and you close the Chromium OS window. It's always better to use the appropriate options on the application's side of things if possible, though (`xiwi gvim -f` in this example).

You can pass parameters to your application like normal, but remember that if you use the combined form (e.g., `sudo enter-chroot xiwi gimp`), your current directory is ignored.  So even if you're in your Downloads folder and want to edit img.jpg, `sudo enter-chroot -b xiwi gimp ~/Downloads/img.jpg` is necessary.

Single-window applications work better than multi-window applications, but if you end up with multiple windows, you can switch between them via either the tabs at the top, or via the <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Tab</kbd> and <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Shift</kbd>+<kbd>Tab</kbd> keyboard shortcuts.  <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Shift</kbd>+<kbd>Escape</kbd> acts the same as the close button on most windows.  If a pop-up window appears in a weird place, you can either drag-move it, or <kbd>Alt</kbd>+<kbd>'Drag'</kbd> to resize it.

If you launch applications via aliases, the xiwi command will not know about them.  You can add the following snippet to your .bashrc/.zshrc to handle basic aliases:

```
# Wrap xiwi so that aliases work
xiwi() {
    local xiwiargs=''
    while [ "${1#-}" != "$1" ]; do
        xiwiargs="$xiwiargs $1"
        shift
    done
    local cmd="`alias "$1" 2>/dev/null`"
    if [ -z "$cmd" ]; then
        cmd="$1"
    else
        eval "cmd=${cmd#*=}"
        cmd="env $cmd"
    fi
    shift
    eval "/usr/local/bin/xiwi $xiwiargs $cmd \"\$@\""
}
```