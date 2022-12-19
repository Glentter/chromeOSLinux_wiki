EDIT: The procedure below no longer works: VideoLan has withdrawn the links! The only way (or at least the easiest way) is to install libdvd-pkg. This works for xenial and stretch; unfortunately, the package is not available in trusty. You can add xenial sources to trusty, pin them at 100, and then execute:

`sudo apt-get install libdvd-pkg/xenial`

What this does, is downloads the libdvdcss-2 code, and compiles it using only dependencies from trusty, thus not messing up your trusty install.


***


The following procedure should work for any debian or ubuntu release. Go to the following page:

[https://www.videolan.org/developers/libdvdcss.html](https://www.videolan.org/developers/libdvdcss.html)  

Follow the instructions to add 2 new lines to your `/etc/apt/sources.list` and then download the verification key. If you have the crouton extension, you can easily copy and paste to accomplish this.

The only trick is knowing the proper package name. Here is what you need to do after installing the key:  

    sudo apt-get update  
    sudo apt-get install libdvdcss2  

And now you're done!