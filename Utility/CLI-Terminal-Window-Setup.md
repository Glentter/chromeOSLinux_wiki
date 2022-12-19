This doc is about how to set up a customizable terminal window that doesn't have to run inside a Chrome tab. It is a guide using several Chrome extensions in tandem to create an authentic terminal app running inside Chrome OS. It assumes that you have a CLI installation of Crouton, and you wish to run it alongside the Chrome browser. There are many bits and pieces, but I found a very useful setup for my CLI Crouton chroot that I thought worth sharing with others. It serves as a good complement to the doc [CLI Development Environment](https://github.com/dnschneid/crouton/wiki/CLI-Development-Environment), which focuses on implementing [Caret](https://chrome.google.com/webstore/detail/caret/fljalecfjciodhpcledpamjachpmelml) as a text editor.

![Crouton CLI running in Terminal window](https://github.com/brentirwin/images/blob/master/image.png?raw=true)

## Secure Shell Extension

The [Secure Shell Extension](https://chrome.google.com/webstore/detail/secure-shell-extension/iodihamcpbpeioajjeobimgagajmlibd) claims to be a terminal emulator and SSH client for Chrome OS, but it is nowhere near as functional as your Crouton. This extension is a prerequisite for the Crosh Window Extension, and it allows you to customize your terminal window as well.

When you install this extension, right-click on its icon and select Options. This is where you can customize the look of your terminal. For the setup used in the image, I added "Droid Sans Mono Dotted" as the first item in the "font-family" line, and pasted the following in "color-pallette-overrides" to give it a [Solarized Dark](http://ethanschoonover.com/solarized) color theme:

```
[
  "#073642",
  "#dc322f",
  "#859900",
  "#b58900",
  "#268bd2",
  "#d33682",
  "#2aa198",
  "#eee8d5",
  "#002b36",
  "#cb4b16",
  "#586e75",
  "#657b83",
  "#839496",
  "#6c71c4",
  "#93a1a1",
  "#fdf6e3"
]
```

Maks sure "environment" contains the line `"TERM": "xterm-256color"`. I found the settings "copy-on-select" and "mouse-right-click-paste" to be very useful. 

Note that the alpha slider on "background-color" does not appear to work with Crosh Window, and it should be kept at 100%, or the background color will default to black. Also note that changing the "font-size" here will not change the integrity of your Crosh Window later, as it defaults to an 80x24 window regardless of font size.

## Crosh Window Extension

The next extension to install is the [Crosh Window Extension](https://chrome.google.com/webstore/detail/crosh-window/nhbmpbdladcchdhkemlojfjdknjadhmh). This is the application to pin to your shelf for easy access. Opening this applicaiton will launch Crosh in an isolated terminal window, outside of your Chrome tabs, using the settings you chose in Secure Shell Options. To open the terminal without moving your hands from the keyboard, type `🔍 crosh [Enter]`. (This will work after opening Crosh Window more often than Crosh, and Google will predict Crosh Window as the top choice.)

Then as always to get into it, type
```
crosh> shell
chronos@localhost / $ sudo startcli
```
to enter your Crouton chroot.

## Fixing the color scheme

The color scheme you chose probably will not be working right off the bat, so you have to make sure your Bash profile is using xterm. Add the following line anywhere in your `~/.bashrc` file.
```
export TERM=xterm-256color
```
Save the file and refresh the Bash profile (`$ source ~/.bashrc`). All of the color issues that were present (and they were even worse in tmux) are now fixed, and you're ready to go!

## Root directory

The default directory you'll find yourself in the root directory which only contains a single directory, Downloads. It can be tempting to do your work here in the root directory, but anything you do outside of that Downloads folder will not be accessible in Chrome OS. If you're like me, your Downloads folder is a horrible mess, and you don't want to navigate through it in the command line either. Using symbolic links, you can create easy-to-access directories that live in your Downloads folder, but you don't have to fool with it to find them.

In my case, I have 2 directories: dev, school, and txt for development projects, schoolwork, and text files I keep. First, create the directory inside of downloads, either in the Chrome OS file navigator or using `$ mkdir ~/Downloads/dev`. Then create a symbolic link:
```
$ ln -s ~/Downloads/dev ~/dev
```

Now when you boot up your new terminal window, you will be able to navigate directly to your dev directory without worrying about the messy Downloads folder. But if you need to access the files in Chrome OS, say, to email them to someone else, simply find the dev folder located nicely inside of Downloads! Repeat as necessary.

## Use

I've found this setup to be almost all I need for most school projects and fully capable for web development. If you're comfortable using Vim, you're ready to go. If you'd like a more traditional text editor, check out [this other page in the wiki](https://github.com/dnschneid/crouton/wiki/CLI-Development-Environment). This chroot is fully capable of running Node.js or Rails, and pointing your Chrome browser to localhost:<PORT> will work as expected.

In my experience, giving myself a fully-functional Ubuntu terminal using this setup has turned my Chromebook from a cheap web browsing machine into a lightweight, capable, web and CLI development machine.

## Closing

When closing the Crosh Window, be sure to do it properly. Type `exit` to exit the Crouton chroot, `exit` to exit the shell, and `exit` to exit Crosh. This final exit will close the window. Closing the window via the X in the title bar may cause processes to continue running in the background that won't be accessible via `ps` in another window. Be sure to also use multiple `exit`s to close the window.