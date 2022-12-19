ChromeOS can run a lot of Chrome processes. If you are working primarily in your crouton chroot, then this can place an excessive load on the system.

I've found that it is possible to kill a lot of these Chrome processes without doing anything damaging (though you will lose any unsaved data on websites in Chrome). These should be the ChromeOS chrome processes: <code>/opt/chrome/chrome/chrome</code>
Try this

  <pre>ps ax -o sess,pgrp,pid,cmd | awk '$4 ~ /\/chrome$/ { print $3 }' | xargs $nodo kill</pre>

Note that if you are running Chrome in your chroot, it *may* (I haven't tested it) be killed.

PS: if you set a variable <code>nodo=echo</code> then it is a dry-run. 