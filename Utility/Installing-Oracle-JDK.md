Mon Jan 23 13:38:16 PST 2023
### Note: For this guide I'll be focusing on ARM Chromebooks and assuming the latest version is jdk1.7.0_60, you may have to update the commands in this guide to match the current version number for the JDK you download. These instructions are based on the intel instructions found on [wikihow](http://www.wikihow.com/Install-Oracle-Java-on-Ubuntu-Linux).

## Downloading Oracle Java
Download the latest version of Oracle JDK: [https://jdk7.java.net/download.html](https://jdk7.java.net/download.html)

You must make sure you download the correct version for your computer's processor. Users with ARM Chromebooks must use the Linux ARMv6/7 HardFP version.

## Extracting
Note: The paths mentioned can be changed depending on the distro or how you've structured your file system. I'll be using the defaults for Ubuntu 12.04 and 13.10.

From your download directory:

```
tar -xvzf jdk-7u60.tar.gz
sudo mv jdk1.7.0_60 /usr/lib/jvm/jdk1.7.0_60
```

## Updating System Settings

The following steps are required to get get your system to point to Oracle Java when running Java applications and compiling.

1. Update /etc/profile using a text editor (ie: nano, gedit, emacs, vi, kate)

Add the following lines to the bottom of /etc/profile (or update any lines if a previous version of Oracle Java was installed):

```
JAVA_HOME=/usr/lib/jvm/jdk1.7.0_60
PATH=$PATH:$HOME/bin:$JAVA_HOME/bin
JRE_HOME=/usr/lib/jvm/jdk1.7.0_60/jre
PATH=$PATH:$HOME/bin:$JRE_HOME/bin
export JAVA_HOME
export JRE_HOME
export PATH
```

Note: after editing /etc/profile. type the following to apply changes:

`. /etc/profile`

Typing `java -version` should then show your current version of java

2. Update Aternatives

Note: These are the manual commands for updating the java and javac alternatives. These changes are included in the advanced section below and don't need to be followed if you follow the advanced instructions.

The following alternatives must be changed at minimum:
```
sudo update-alternatives --install "/usr/bin/java" "java" "/usr/lib/jvm/jdk1.7.0_60/jre/bin/java" 1;
sudo update-alternatives --set "java" "/usr/lib/jvm/jdk1.7.0_60/jre/bin/java";
sudo update-alternatives --install "/usr/bin/javac" "javac" "/usr/lib/jvm/jdk1.7.0_60/bin/javac" 1;
sudo update-alternatives --set "javac" "/usr/lib/jvm/jdk1.7.0_60/bin/javac";
```

This is all that's required for simple compiling.

# Advanced Steps (for packages that rely on default-java, ie: Eclipse)

Install the package default-jre-headless:

`sudo apt-get install default-jre-headless`

Update default java and update-java-alternatives to point to jdk1.7.0_60 (default is openjdk7)

```
cd /usr/lib/jvm;
sudo rm default-java;
sudo ln -s jdk1.7.0_60 default-java;
```

The next step depends on if jinfo files exist from openjdk installations.
From /usr/lib/jvm:

`ls -al` 

look for files that start with a "." and end with ".jinfo", they should be named after openjdk versions if they exist. You'll have to rename these to something that doesn't end with .jinfo, such as:
 renaming ".java-1.7.0-openjdk-armhf.jinfo" to "jinfo.java-1.7.0-openjdk-armhf".

`sudo mv .java-1.7.0-openjdk-armhf.jinfo jinfo.java-1.7.0-openjdk-armhf`

You'll then need to generate a .jinfo file for Oracle Java 1.7.0_60. You can do this manually based on the one you copied over, just name it to match your jdk version then add ".jinfo", or copy the version below and modify the java version.

Note for ARM: You must remove the bottom line for "plugin" from the .jinfo if you're copying another .jinfo, because the arm version doesn't include a web plugin, or javaws.

## .jdk1.7.0_60.jinfo
```
name=jdk1.7.0_60
priority=2014
section=main
hl java /usr/lib/jvm/jdk1.7.0_60/jre/bin/java
hl keytool /usr/lib/jvm/jdk1.7.0_60/jre/bin/keytool
hl pack200 /usr/lib/jvm/jdk1.7.0_60/jre/bin/pack200
hl rmid /usr/lib/jvm/jdk1.7.0_60/jre/bin/rmid
hl rmiregistry /usr/lib/jvm/jdk1.7.0_60/jre/bin/rmiregistry
hl unpack200 /usr/lib/jvm/jdk1.7.0_60/jre/bin/unpack200
hl orbd /usr/lib/jvm/jdk1.7.0_60/jre/bin/orbd
hl servertool /usr/lib/jvm/jdk1.7.0_60/jre/bin/servertool
hl tnameserv /usr/lib/jvm/jdk1.7.0_60/jre/bin/tnameserv
hl jexec /usr/lib/jvm/jdk1.7.0_60/jre/lib/jexec
jre policytool /usr/lib/jvm/jdk1.7.0_60/jre/bin/policytool
jdk appletviewer /usr/lib/jvm/jdk1.7.0_60/bin/appletviewer
jdk extcheck /usr/lib/jvm/jdk1.7.0_60/bin/extcheck
jdk idlj /usr/lib/jvm/jdk1.7.0_60/bin/idlj
jdk jar /usr/lib/jvm/jdk1.7.0_60/bin/jar
jdk jarsigner /usr/lib/jvm/jdk1.7.0_60/bin/jarsigner
jdk javac /usr/lib/jvm/jdk1.7.0_60/bin/javac
jdk javadoc /usr/lib/jvm/jdk1.7.0_60/bin/javadoc
jdk javah /usr/lib/jvm/jdk1.7.0_60/bin/javah
jdk javap /usr/lib/jvm/jdk1.7.0_60/bin/javap
jdk jcmd /usr/lib/jvm/jdk1.7.0_60/bin/jcmd
jdk jconsole /usr/lib/jvm/jdk1.7.0_60/bin/jconsole
jdk jdb /usr/lib/jvm/jdk1.7.0_60/bin/jdb
jdk jhat /usr/lib/jvm/jdk1.7.0_60/bin/jhat
jdk jinfo /usr/lib/jvm/jdk1.7.0_60/bin/jinfo
jdk jmap /usr/lib/jvm/jdk1.7.0_60/bin/jmap
jdk jps /usr/lib/jvm/jdk1.7.0_60/bin/jps
jdk jrunscript /usr/lib/jvm/jdk1.7.0_60/bin/jrunscript
jdk jsadebugd /usr/lib/jvm/jdk1.7.0_60/bin/jsadebugd
jdk jstack /usr/lib/jvm/jdk1.7.0_60/bin/jstack
jdk jstat /usr/lib/jvm/jdk1.7.0_60/bin/jstat
jdk jstatd /usr/lib/jvm/jdk1.7.0_60/bin/jstatd
jdk native2ascii /usr/lib/jvm/jdk1.7.0_60/bin/native2ascii
jdk rmic /usr/lib/jvm/jdk1.7.0_60/bin/rmic
jdk schemagen /usr/lib/jvm/jdk1.7.0_60/bin/schemagen
jdk serialver /usr/lib/jvm/jdk1.7.0_60/bin/serialver
jdk wsgen /usr/lib/jvm/jdk1.7.0_60/bin/wsgen
jdk wsimport /usr/lib/jvm/jdk1.7.0_60/bin/wsimport
jdk xjc /usr/lib/jvm/jdk1.7.0_60/bin/xjc
```

## Advanced (Cont.) installing alternatives
For each of the items you add to .jinfo above you need to add install a link in update-alternatives. The following set of commands would match the above file:

## installAlternatives.sh
```
sudo update-alternatives --install "/usr/bin/java" "java" "/usr/lib/jvm/jdk1.7.0_60/jre/bin/java" 2014;
sudo update-alternatives --install "/usr/bin/keytool" "keytool" "/usr/lib/jvm/jdk1.7.0_60/jre/bin/keytool" 2014;
sudo update-alternatives --install "/usr/bin/pack200" "pack200" "/usr/lib/jvm/jdk1.7.0_60/jre/bin/pack200" 2014;
sudo update-alternatives --install "/usr/bin/rmid" "rmid" "/usr/lib/jvm/jdk1.7.0_60/jre/bin/rmid" 2014;
sudo update-alternatives --install "/usr/bin/rmiregistry" "rmiregistry" "/usr/lib/jvm/jdk1.7.0_60/jre/bin/rmiregistry" 2014;
sudo update-alternatives --install "/usr/bin/unpack200" "unpack200" "/usr/lib/jvm/jdk1.7.0_60/jre/bin/unpack200" 2014;
sudo update-alternatives --install "/usr/bin/orbd" "orbd" "/usr/lib/jvm/jdk1.7.0_60/jre/bin/orbd" 2014;
sudo update-alternatives --install "/usr/bin/servertool" "servertool" "/usr/lib/jvm/jdk1.7.0_60/jre/bin/servertool" 2014;
sudo update-alternatives --install "/usr/bin/tnameserv" "tnameserv" "/usr/lib/jvm/jdk1.7.0_60/jre/bin/tnameserv" 2014;
sudo update-alternatives --install "/usr/bin/jexec" "jexec" "/usr/lib/jvm/jdk1.7.0_60/jre/lib/jexec" 2014;
sudo update-alternatives --install "/usr/bin/policytool" "policytool" "/usr/lib/jvm/jdk1.7.0_60/jre/bin/policytool" 2014;
sudo update-alternatives --install "/usr/bin/appletviewer" "appletviewer" "/usr/lib/jvm/jdk1.7.0_60/bin/appletviewer" 2014;
sudo update-alternatives --install "/usr/bin/extcheck" "extcheck" "/usr/lib/jvm/jdk1.7.0_60/bin/extcheck" 2014;
sudo update-alternatives --install "/usr/bin/idlj" "idlj" "/usr/lib/jvm/jdk1.7.0_60/bin/idlj" 2014;
sudo update-alternatives --install "/usr/bin/jar" "jar" "/usr/lib/jvm/jdk1.7.0_60/bin/jar" 2014;
sudo update-alternatives --install "/usr/bin/jarsigner" "jarsigner" "/usr/lib/jvm/jdk1.7.0_60/bin/jarsigner" 2014;
sudo update-alternatives --install "/usr/bin/javac" "javac" "/usr/lib/jvm/jdk1.7.0_60/bin/javac" 2014;
sudo update-alternatives --install "/usr/bin/javadoc" "javadoc" "/usr/lib/jvm/jdk1.7.0_60/bin/javadoc" 2014;
sudo update-alternatives --install "/usr/bin/javah" "javah" "/usr/lib/jvm/jdk1.7.0_60/bin/javah" 2014;
sudo update-alternatives --install "/usr/bin/javap" "javap" "/usr/lib/jvm/jdk1.7.0_60/bin/javap" 2014;
sudo update-alternatives --install "/usr/bin/jcmd" "jcmd" "/usr/lib/jvm/jdk1.7.0_60/bin/jcmd" 2014;
sudo update-alternatives --install "/usr/bin/jconsole" "jconsole" "/usr/lib/jvm/jdk1.7.0_60/bin/jconsole" 2014;
sudo update-alternatives --install "/usr/bin/jdb" "jdb" "/usr/lib/jvm/jdk1.7.0_60/bin/jdb" 2014;
sudo update-alternatives --install "/usr/bin/jhat" "jhat" "/usr/lib/jvm/jdk1.7.0_60/bin/jhat" 2014;
sudo update-alternatives --install "/usr/bin/jinfo" "jinfo" "/usr/lib/jvm/jdk1.7.0_60/bin/jinfo" 2014;
sudo update-alternatives --install "/usr/bin/jmap" "jmap" "/usr/lib/jvm/jdk1.7.0_60/bin/jmap" 2014;
sudo update-alternatives --install "/usr/bin/jps" "jps" "/usr/lib/jvm/jdk1.7.0_60/bin/jps" 2014;
sudo update-alternatives --install "/usr/bin/jrunscript" "jrunscript" "/usr/lib/jvm/jdk1.7.0_60/bin/jrunscript" 2014;
sudo update-alternatives --install "/usr/bin/jsadebugd" "jsadebugd" "/usr/lib/jvm/jdk1.7.0_60/bin/jsadebugd" 2014;
sudo update-alternatives --install "/usr/bin/jstack" "jstack" "/usr/lib/jvm/jdk1.7.0_60/bin/jstack" 2014;
sudo update-alternatives --install "/usr/bin/jstat" "jstat" "/usr/lib/jvm/jdk1.7.0_60/bin/jstat" 2014;
sudo update-alternatives --install "/usr/bin/jstatd" "jstatd" "/usr/lib/jvm/jdk1.7.0_60/bin/jstatd" 2014;
sudo update-alternatives --install "/usr/bin/native2ascii" "native2ascii" "/usr/lib/jvm/jdk1.7.0_60/bin/native2ascii" 2014;
sudo update-alternatives --install "/usr/bin/rmic" "rmic" "/usr/lib/jvm/jdk1.7.0_60/bin/rmic" 2014;
sudo update-alternatives --install "/usr/bin/schemagen" "schemagen" "/usr/lib/jvm/jdk1.7.0_60/bin/schemagen" 2014;
sudo update-alternatives --install "/usr/bin/serialver" "serialver" "/usr/lib/jvm/jdk1.7.0_60/bin/serialver" 2014;
sudo update-alternatives --install "/usr/bin/wsgen" "wsgen" "/usr/lib/jvm/jdk1.7.0_60/bin/wsgen" 2014;
sudo update-alternatives --install "/usr/bin/wsimport" "wsimport" "/usr/lib/jvm/jdk1.7.0_60/bin/wsimport" 2014;
sudo update-alternatives --install "/usr/bin/xjc" "xjc" "/usr/lib/jvm/jdk1.7.0_60/bin/xjc" 2014;
```

## Advanced (Cont.) Now to set the oracle version as the default for all of these.

`sudo update-java-alternatives -s jdk1.7.0_60`

This should set Oracle Java as the default for all java commands and for the java-default package, without these steps openjdk will remain the default java version for certain builds.

## Confirming the changes.

Run the following command, it should show all Java executables and if they're pointing to the correct directory.

`update-alternatives --get-selections | grep java`

