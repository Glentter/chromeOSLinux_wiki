crouton's clipboard synchronization architecture consists of the following components:
 - extension: A Chromium extension that connects to a localhost websocket server (port 30001).
   - If the server does not respond, it retries every 5 seconds.
   - Once connected, it checks the server version, then listens for incoming commands.
 - crouton-websocket: A websocket server (one process only, no matter how many instances of crouton are running).
   - Accepts connections from extension. It technically supports multiple connection, but only the last connected client gets data.
   - Reads data from a FIFO input pipe (`/tmp/crouton-websocket-in`), and forwards requests to the extension. Sends back data on a FIFO output pipe (`/tmp/crouton-websocket-out`).
 - croutonclip (again, one process only): A script that monitors for VT change (x11), or active window change (xephyr), and copies around the clipboard data using either `xclip` or crouton-websocket FIFOs. Its design must be guaranteed to be safe, so that rapid X server rotations are processed in sequence.

### Websocket/FIFO protocol

Connection initialization:
 - Version check: crouton-websocket sends `V2`, extension replies with `VOK`, or error (`E...`).

Most commands are initiated from the chroot (via FIFO). crouton-websocket only does forwarding in this case). Commands are in the form `?<payload>`, and replies must either be `?<reply>`, in case of success, or `E...` in case of error. Only replies in these formats are written back to the FIFO (crouton-websocket blocks until such a reply is receied).

 - `E<message>`: Errors, can be sent directly from crouton-websocket (i.e., extension not connected), or from the extension itself.
 - `W<payload>`: Write (or paste to Chromium). Reply: `WOK`.
 - `R`: Read (or copy from Chromium). Reply: `R<payload>`.
 - `P<payload>`: Ping. Reply: `P<payload>`.
 - `U<url>`: Open URL in Chromium. Reply: `UOK`.
 - `X<:display>`: Ask the extension to open a Kiwi window. Reply: `XOK`.
 - `N<payload>`: Raise a notification in Chromium OS. Reply: `NOK`.

Commands initiated from the extension. Those commands are handled directly in crouton-websocket, and not passed down to the FIFO. These commands can be interleaved with respect to chroot-initiated commands, and must not have a length greater than 4095 bytes (including command character).

 - `Cl`: Executes a `croutoncycle l` command. crouton-websocket replies with `C<command output>`
 - `C<cmd>`: Executes a `croutoncycle <cmd>` command. crouton-websocket replies with `C`.

The websocket protocol is frame-based, so the payload is guaranteed to be transmitted atomically: no incomplete reads/writes. There is no maximum frame length (up to 2**63 bytes...).

FIFOs are a bit trickier to handle, as atomicity is only guaranteed for packets up to 4096 bytes (`PIPE_BUF` value on Linux, see `man 7 pipe`), and multiple writes may still appear as a single command (e.g. if croutonclip write `Wa` then `Wb`, and the server is busy at the time the first command arrives, it may erroneously read `WaWb`). One could include the packet size after the `W`, but that would increase croutonclip's complexity (remember: it's shell...).

To circumvent this problem, croutonclip must behave as follows:
 - Write data to the FIFO input pipe
 - Read ack/data from the FIFO output pipe.

We also need to make sure that only one process communicates with the server at a time: this can be done by `flock`ing a lock, but it is currently unnecessary as there is only one instance of croutonclip.

On the server side, the FIFO input pipe is in blocking mode: `read` only returns 0 upon EOF (croutonclip is done writing). Ack/data is then written to the output pipe. To prevent from blocking forever if croutonclip does not read the pipe, the server only tries to write the data for 3 seconds. croutonclip should also probably timeout.