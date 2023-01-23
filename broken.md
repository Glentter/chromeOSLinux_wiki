
https://github.com/dnschneid/crouton/issues/4026

The internet is wrong and all the tutorials are broken
#4026
Apr 3, 2019 · 40 comments
enhancement
The internet is wrong and all the tutorials are broken ·...

https://github.com/dnschneid/crouton/issues/4026
dnschneid commented on Apr 3, 2019 • edited
Owner
An improvement in Chromium OS security has broken all the internet tutorials for crouton.
Oops.
But it's OK, with the power of the internet, we can ﬁx the internet!
If you've found a tutorial that is still listing the old commands ( sudo sh ~/Downloads/crouton
... ), please reach out to the author (nicely!) and ask them to update.
What used to be the following steps (these are now wrong!):
1. Download crouton
2. Run sudo sh ~/Downloads/crouton ...
Have become the equally-memorable:
1. Download crouton
2. Prep the installer with sudo install -Dt /usr/local/bin -m 755 ~/Downloads/crouton
3. Run sudo crouton ...
The steps for running chroots oﬀ of external media have also become more convoluted. It
would be best if tutorials just referred to the README section in that case.
If you ﬁnd the old steps are still working, it's only because the changes haven't rolled out to you
yet. It will. Just you wait. Or don't wait, and instead help make crouton tutorials great again.
Post links here to tutorials you've helped correct for some sweet, sweet recognition!

dnschneid pinned this issue on Apr 3, 2019

justchen1369 commented on Apr 4, 2019 • edited
Looks like a good idea is to notify the owners of any broken tutorials and (?) tell them to add the
extra step of moving the crouton script to /usr/local.

DennisLfromGA commented on Apr 4, 2019
Collaborator
@justchen1369
The README has been updated, everybody reads that right?
Please read the relevant sections of this README carefully, and reach out to your favorite
weblogger/tutorialer/videotuber to update their guides if they're behind the times. If
you're successful, brag about your accomplishments in the issue tracker and earn the
personal gratitude of the crouton authors*!
The internet is wrong and all the tutorials are broken ·...


https://github.com/dnschneid/crouton/issues/4026
personal gratitude of the crouton authors*!
• limit one (1) gratitude per commenter
-DennisLfromGA
justchen1369 commented on Apr 4, 2019
Just sent a message to the author of the HowToGeek article on crouton.

LynWales commented on Apr 4, 2019
It is really pleasing to hear that Crouton is continuing-I had feared it's demise as Crostini was
introduced -Are there any plans to add more up to date Targets-The last Ubuntu distribution I
can see is xenial and there have been several new rollouts since then?
justchen1369 commented on Apr 5, 2019
Buster is available- just plop a -r buster in the command(and others are probably available)

JL2210 commented on Apr 5, 2019
What changed? Can't we just mount -o remount the home drive temporarily?

dnschneid commented on Apr 5, 2019
Owner
Author
That would technically work, but it would also bypass many layers of protection in Chromium
that prevents random downloaded ﬁles (including cached extensions and whatnot) from being
executable.
As for what changed, see the ﬁrst link in this bug.

justchen1369 commented on Apr 6, 2019
The internet is wrong and all the tutorials are broken ·...

This was referenced on Apr 10, 2019
UID 1000 not found in bionic #4035
Closed
Can't open "sudo sh ~/Downloads/crouton -r trusty -t xfce" #4037
Closed

ErikLentz commented on Apr 12, 2019
I updated My blog post on setting up Crouton and Steam! It's my most traﬃcked article, and I
can always tell when people have been buying new Chromebooks by the stats bump.
Thanks for the easy steps!


justchen1369 commented on Apr 12, 2019
thanks for updating it!

dnschneid commented on May 9, 2019

@smeggysmeg thanks! looks like you need to update the "how to update" steps as well.
ErikLentz commented on May 9, 2019
Woops. Fixed!

radumas mentioned this issue on May 11, 2019
moving chroot onto usb storage? #960
Closed

pardestakal commented on May 11, 2019
when i try to install cinnamon it doesnt work cuz there is no target, and theres no support for
bionic when i do sudo crouton -t touch,kde -r bionic. it would be cool if you could add support
for it cuz most apps i need, need bionic. but either way thanks this is amazing

binMonkey commented on Jun 18, 2019
Does this change help with apt/update/segmentation fault/sig 4 problem?
Thanks for this and all the work you do.

binMonkey mentioned this issue on Jun 18, 2019
Method http has died unexpectedly during software installation #2688
Closed

pjchamberlain commented on Jun 18, 2019
sadly #2688 is still an issue

binMonkey commented on Jun 18, 2019
I can verify that this is a problem in Debian Stretch also. It was working perfectly for two hours


I can verify that this is a problem in Debian Stretch also. It was working perfectly for two hours
and then it went to pot.

DennisLfromGA mentioned this issue on Jul 17, 2019
uid 1000 not found for all chroots #4095


ApatheticEupho… commented on Jul 17, 2019 • edited
I followed the updated instructions and still receive uid error. I think it may have something to
do with my speciﬁc chromebook because it works on my Dell chromebook just ﬁne. Perhaps I
messed something up early on and now it wont work even following the updated method? Or
maybe it simply isn't compatible with this version. Please see my issue for build information.
Would really like to get Kali on this machine.
If it is relevant I tried and failed to root using Aroc.
irishgreencitrus mentioned this issue on Jul 26, 2019
UID 1000 not found in any chroots #4107


jvschiavo commented on Sep 19, 2019
Hi, I would like to understand what the command sudo install -Dt /usr/local/bin -m 755
~/Downloads/crouton does.
ImpatientHippo commented on Sep 19, 2019 • edited
This installs the crouton script to /usr/local/bin with appropriate access rights.
See: https://linux.die.net/man/1/install

jvschiavo commented on Sep 19, 2019
This installs the crouton script to /usr/local/bin with appropriate access rights.
See: https://linux.die.net/man/1/install
So... is it like sudo cp ~/Downloads/crouton /usr/local/bin then sudo chmod 755 /usr/local
/bin/crouton ?

JL2210 commented on Sep 19, 2019
@jvschiavo Yes.
1
jvschiavo commented on Sep 22, 2019
@jvschiavo Yes.
Thanks! :)

jcd83a mentioned this issue on Nov 10, 2019
Downloading Linux on Chromebook; cannot run command "sudo startxfce4".
Please Help! #4131

jcd83a mentioned this issue on Nov 10, 2019
Trouble with 'sudo startxfce4' after linux installation on chromeOS #4166


foxsouns commented on Jan 24, 2020
https://github.com/dnschneid/crouton/wiki/Keyboard requires an update.
DennisLfromGA commented on Jan 24, 2020
Collaborator
@itstoast27,
The wikis are editable for all so you can add the keyboard updates yourself or you can ﬁle an
issue that needs wiki entries or updates so others can update them.
Hope this helps,
-DennisLfromGA
foxsouns commented on Jan 24, 2020 • edited


foxsouns commented on Jan 24, 2020 • edited
@DennisLfromGA
The wikis are editable for all so you can add the keyboard updates yourself or you can ﬁle
an issue that needs wiki entries or updates so others can update them. <
Ah, I wasn't aware. I'll probably update it when I'm ﬁnished with what I'm doing, thank you.


jimmybungalo commented on Mar 22, 2020
when i try to install cinnamon it doesnt work cuz there is no target, and theres no support
for bionic when i do sudo crouton -t touch,kde -r bionic. it would be cool if you could add
support for it cuz most apps i need, need bionic. but either way thanks this is amazing
it is likely it doesn't need bionic, ubuntu updates are updates to the os, support for packages
and ﬁles that you install are based on debian, and some packages do not work with older
versions (jessie, stretch), and require new ones (buster, sid(unstable)). if you really wanted to,
you could install debian buster straight oﬀ instead of using it through linux, but you must know
your way around a debian environment.

drxjason commented on Apr 15, 2020
Some dependencies on bionic are just not found and some people are unable to fully install
bionic onto their systems...

JonasBBX commented on May 2, 2020
Some dependencies on bionic are just not found and some people are unable to fully install
bionic onto their systems...
same here

DennisLfromGA commented on May 3, 2020
@GDJason12 and @jonasthenoob,
bionic issues are not the subject of this topic.
Please search for existing 'bionic dependency' issues and if you can't ﬁnd any then open a new
issue.
Thanx,
-DennisLfromGA


levidoughty commented on Jun 24, 2020 • edited
i just dm'ed the author of this article on twitter. (i found his twitter through a google search, as it
wasn't listed on linux.com.) i ﬁlled him in on the changes and linked him to this page, so i hope
that this is useful, even in the tiniest way!

dnschneid commented on Jun 24, 2020

Awesome! Thank you, @swagbob6!

makebabynotwar commented on Dec 6, 2020
Dear Mr. Author, goo.gl/fd3zc <- this link is not working anymore, I can't download the crouton
installer anymore. I hope you could ﬁx this soon. Thank you!

makebabynotwar mentioned this issue on Dec 6, 2020
Dear Mr. Author, goo.gl/fd3zc <- this link is not working anymore, I can't
download the crouton installer anymore. I hope you could ﬁx this soon. Thank
you! #4383


imarisimpson commented on Mar 19, 2021
So like did you guys change them again or something because I am getting a new error

azet commented on May 16, 2021
Contributor
How about adding a mention of noexec mount to the title of this issue? This way it would be
easy to ﬁnd.

foxsouns commented on May 16, 2021
+1 for that mention of noexec mount; that way it'd have the words that someone having that
error would be looking for, and the solution would be right here for them to look at. Maybe
something like the following?

something like the following?
The internet is wrong and all the tutorials are broken (addressing the "from noexec
mount" error)

ﬂexhornmoring… commented on Jul 2, 2021
whats this all about seeing dead threads on the issue.

BetaPictoris commented on Jul 2, 2021
@ﬂexhornmoringstar
whats this all about seeing dead threads on the issue.
The internet is wrong and all the tutorials are broken ·...

. Maybe look at this comment from @DennisLfromGA.
ﬂexhornmoring… commented on Jul 2, 2021 via email

BetaPictoris mentioned this issue on Jul 3, 2021
Crouton not installing #3724
Closed
BetaPictoris mentioned this issue on Sep 22, 2021

!¡!¡!¡!crosh dont want to open crouton, help please!!¡!¡!¡! #4612
Closed

DennisLfromGA added the enhancement label on Dec 31, 2021

Repository owner locked as resolved and limited conversation to collaborators on Dec 31, 2021
Assignees
No one assigned
Labels
enhancement
Projects
None yet

Milestone
No milestone
Development
No branches or pull requests
22 participants
and others

