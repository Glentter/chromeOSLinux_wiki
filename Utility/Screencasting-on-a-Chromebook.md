## _... via crouton ..._

**UPDATE**: This is no longer viable with every device on [freon](https://github.com/dnschneid/crouton/issues/1567#issuecomment-88235073) now ( and no `host-x11`) so it's dead Jim...

~This feature has been requested many times at Chromebook Central and probably many other places and the answer in the past has always been to create a solo session of Google 'Hangouts On Air'.~ 

~Now, with the help of Googler [**Francois Beaufort**](https://plus.google.com/+FrancoisBeaufort/ "Screencasting on your Chromebook") and his [_**How-To**_](https://plus.google.com/+FrancoisBeaufort/posts/3fVoMgf6rCZ), it can be done via 'crouton'.~

~Check it out:~

~<center>![Screencasting via crouton](screencasting.png)</center>~

***

~If you'd like to use a *slightly* modified version of 'screencast.sh' with some command line options added, checkout '[screencast](https://gist.github.com/DennisLfromGA/8441689)'.~

~To obtain the file 'screencast', in a cros shell (crosh) do:~
~curl -Lk --connect-timeout 60 -m 300 --retry 2 "https://gist.github.com/DennisLfromGA/8441689/download"~ ~|~ ~tar -xzO > ~/Downloads/screencast~
