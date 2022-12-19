***
The _**SME Storage Made Easy**_ product is a very handy thing when loaded in a chroot.

You get a free Amazon S3 (5GB) account from SMES when signing up for the free Personal account and up to 3 more other Cloud Providers can be linked in, also with the free Personal account.
For the Cloud Providers, there are several Google products (Google Drive, Google Docs, Gmail, etc.), Amazon S3, Dropbox, Box.net, CloudMe, Office 365, SkyDrive, etc.

There's even a _**Chrome Web App**_ that has a very robust set of features - it's not just a hyper-link.
The link is here -

  **'[Storage Made Easy Cloud Dashboard Pro](https://chrome.google.com/webstore/detail/storage-made-easy-cloud-d/fifbddboijfpcjflfocelaelpbefjbha)'**

![SME Cloud Dashboard Pro App](smes-images/6.png)

~~If you're looking for a quick & easy guide, it's already been done at the _**'Ubuntu in The Wild'**_ site here -~~

  ~~**'[Merge cloud storage on Ubuntu using SME](http://ubuntuinthewild.blogspot.com/2012/05/merge-cloud-storage-on-ubuntu-using-sme.html)'**~~

**Sorry, the '*Ubuntu in The Wild*' aritcle/site has been taken down apparently.**

If you'd like a little more guidance, just read on...

***

### _STEP-BY-STEP INSTALLATION INSTRUCTIONS_:

_Part 1 - In the Browser_

* Goto the StorageMadeEasy site - http://storagemadeeasy.com/

* Scroll down near the bottom of the page and click on the link that says: _'Click here for Personal Cloud and Sync solutions'_.

![Personal Cloud and Sync solutions](smes-images/1a.png)

*  Scroll down to the middle of the page that shows _'Cloud Apps for your Desktop'_ and click on the link that says: 'Linux Cloud Drive'

* Once you're on this page you can read about all the features and benifits of SMEStorage and decide if it's something you need or want. If so, then scroll down to the middle of the page and click on the _'Download'_ link for your distribution, most likely Ubuntu or Debian - it's actually the same [.deb](https://storagemadeeasy.com/files/a1ab4ef2bc8a4323d2e186cfdfa4a779.deb) file. Make sure it's saved in the ~/Downloads folder so you can access it from your chroot later.

![Download *nix tools](smes-images/2.png)

* At this point, you can register for a free SMES account by scrolling back up to the top of the page and clicking on the link that says: 'Register', just to the left of 'Sign In' which you'll use later. In the area that shows 'FREE Cloud Packages' you click on the 'Sign Up' button under +3 clouds, it's not highlighted until you hover over it.

![Registration / Sign-up](smes-images/1b.png)

* Complete the User Registration form after double-ckecking that the Selected package is the Free one.

![Registration form](smes-images/3.png)

* Once submitted, you should receive an email message with a link to activate your account. After this, you should get a 2nd email message showing your login name and telling you what to do next with some helpful links. ( You might want to save this 2nd email message because it has a 'reset password' link that you can use if you forget your password. ) They recommend you at least read a wiki page about 'The top 5 things to understand about SMEStorage' here - https://storagemadeeasy.com/wiki/top5things/

* Now it's time to go back to http://storagemadeeasy.com/, sign-in and setup your 'Cloud Providers'. You can do most things from your 'Dashboard' like add providers, change your settings for Sync, File versioning and more.

![Dashboard in browser](smes-images/4.png)

_Part 2 - In the Shell_

* Once you have setup your Cloud Providers, you can drop to a Cros shell and then enter your chroot via your favorite installed Desktop Environment (I.E.) _``'sudo startxfce -n (chrootname)'``. Now in your chroot Desktop, open the Downloads folder in your file browser and select the 'storagemadeeasy_4.0.5.deb' file (or a later version), it should then open up in the Software Center if you have it installed or perhaps Gdebi - if no gui installer opens, you can drop to a shell and type ``'sudo dpkg -i ~/Downloads/storagemadeeasy_4.0.5.deb'``_ to install it from a command line. Now you can use the gui tools, namely 'SME Client', 'SME Explorer', and 'SME Sync' to access your SMES cloud storage.  
**NOTE**: Some individuals have reported problems with dependencies when attempting to install this package and a good tip from [the master](https://github.com/dnschneid) is provided in [this link](https://github.com/dnschneid/crouton/issues/312#issuecomment-24287882). 

![Ubuntu Software Center](smes-images/5.png)

* You can also 'mount' or 'unmount' your SMEStorage folders and access them from a shell command line or a file browser via the script 'sme.mount' available _[here](https://gist.github.com/DennisLfromGA/8105549#file-sme-mount)_. If you want to use the script, either edit it to put in your own credentials and SMEStorage folder location or enter them on the command line - _``'sme.mount Folder_Location Username Password'`` - enter ``'sme.mount -h'``_ for usage. You can copy the 'sme.mount' script to your Downloads folder and use it in both the chroot and Cros shell, respectively -

  _``sh -e ~/Downloads/sme.mount [SMES_Folder_Loc] [SMES_Username] [SMES_Password]``_