Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
# CLI Development Environment
This doc is about how to set up a development environment using a chrome app and a compiler inside crouton. This could be changed to use any editor on chromeos that allows external extension messaging on hotkeys or buttons, as well as any kind of compiler or interpreter on the crouton side. For this example, we will be using [Caret](https://chrome.google.com/webstore/detail/caret/fljalecfjciodhpcledpamjachpmelml) as the editor/IDE and gcc as the compiler inside crouton.

You will need python3 on your crouton instance in order to do this.

The program is made out of 3 parts:
* A trigger inside Caret
* An extension in chrome that does a web call
* A python script in crouton that receives a web call and uses the address in the call to build a file

## Caret Extension
This will allow Caret to tell our extension when an event has happened, and tells our extension what file we are working on.

### External Command
In caret, go to Settings > External Commands.
We will add a new external command.
We name our external command "build". We will use this to trigger it later.
The ID will be the ID from the extension we will create, which can be viewed in the chrome extensions panel.
The message is just information about what this command is, so that we can theoretically distinguish between different calls. Not necessarily needed in this example.
sendEditorContext tells caret to also send the current file location and selected text to our extension, which we want as we want to just build the file we currently have open.
```
{
  "build": {
    "id": "gbkkikigjbfljcghfjngemlfgpjbfhjk",
    "message": {
      "data": "build"
    },
    "sendEditorContext": true
  }
}
```

### Keyboard
We will also add a hotkey to trigger our command inside Caret. You may chose to do this using a menu or another method inside Caret if you like.
At the end of the file, add a command to trigger our build api call
We will trigger off Control+B, you may chose any hotkey that does not conflict with one already existing.

```
...
  //dev mode
  "Ctrl-.": { "command": "app:show-prompt" },
  
  //Build
  "Ctrl-B": { "command": "api:execute", "argument": "build"}
}
```
You can see that our api:execute argument is build, which is the name of our external command above.


## Chrome Extension
Create a new folder in your Downloads folder, mine is called Auto GCC.
In it, create two files: main.js, manifest.json

### manifest.json
The manifest file is the file that tells chrome about the extension. For us it is very simple, the only important thing is the background property, where we define that one of our scripts is a background event script. All other properties are misc properties needed to define any chrome extension.
```
{
  "manifest_version": 2,

  "name": "GCC Auto",
  "description": "Extension to Caret to build GCC on crouton",
  "version": "1.0",
  
  "background": {
    "scripts": ["main.js"],
    "persistent": false
  }
}
```

### main.js
This file contains our logic which connects the chrome extension to our webserver running on crouton.
Firstly, it sends a command to Caret telling it to save the current file first.
This code takes the location of the current file from Caret, then makes a new http request on a port (I chose 8008), and sends a request to that address with the file location appended. We could do this many different ways and probably more effiently with websockets but I'm lazy and its only one call so it won't have any performance impact.
```
chrome.runtime.onMessageExternal.addListener(function(message, sender, sendResponse){
  chrome.runtime.sendMessage("fljalecfjciodhpcledpamjachpmelml", {command:"session:save-file"});
  var fileLocation = message.context.path;
  var xhttp = new XMLHttpRequest();
  xhttp.open("GET", "http://localhost:8008/"+fileLocation, true);
  xhttp.send();
});
```

## Crouton python script
This is a script you will run constantly in the background or in a side window while you program in your Caret window.

Create a new script in your downloads folder (or somewhere else if you desire, you can just use Caret to edit the file if its in your downloads.). I called mine gccserver.py

This file basically sets up a webserver running on localhost:8008 and waits for a request. When it recieves a request, if the url is the right type (a C file), then it will do a call to gcc to build that file, then run it in the shell.

I also added a variable called verbose which you can enable to have a more verbose console telling you when the building, etc, is happening.
```
import http.server
import socketserver
from subprocess import *
import os

verbose = True

class AutoGCCServer(http.server.BaseHTTPRequestHandler):
  def do_GET(self):
    fileLoc = os.getenv("HOME") + self.path[2:]
    if(fileLoc[-1] == "c"):
      do_build(fileLoc)
    else:
      print("Error: {} is not a C file".format(fileLoc))
    self.send_response(200)
    self.send_header('Access-Control-Allow-Origin', self.headers.get('origin'))
    self.send_header('Access-Control-Allow-Methods', 'POST, OPTIONS')
    self.end_headers()
    
  def log_message(self, format, *args):
    return


def do_build(fileLoc):
  outputLoc = fileLoc[:-1] + "o"
  if verbose:
    print("Building '{}'".format(fileLoc))
  try:
    check_call(["gcc", fileLoc, "-o", outputLoc])
  except CalledProcessError as e:
    print("Build failed")
    print(e.returncode)
  if verbose:
    print("Executing '{}'".format(outputLoc))
  exited = False
  try:
    check_call([outputLoc])
    print("~Completed without error~")
  except KeyboardInterrupt:
    print("...")
  except CalledProcessError as e:
    print("~Completed with error code {}~".format(e.returncode))


if __name__ == "__main__":
  PORT = 8008
  socketserver.TCPServer.allow_reuse_address = True
  httpd = socketserver.TCPServer(("", PORT), AutoGCCServer)
  try:
    httpd.serve_forever()
  except KeyboardInterrupt:
    pass
  except:
    print("Error Occured.")
  httpd.shutdown()
  print("\nServer stopped")
```

## Putting it all together
We now need to set everything up.

To install the extension, go to Extensions, enable Developer mode, and click Load unpacked extension. Select the extension folder we created and click open.
In a shell window, run the server file with python3 in crouton.

Now, create a C file, write some test code and press your button combination (Ctrl+B).
You should see the shell compile and run your program in the background!