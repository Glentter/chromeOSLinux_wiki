## Build Essential
In order to build a package from source you must first have the build-essential package installed:
`sudo apt-get install build-essential`

## Build Dependencies
Before building a package you will need all the dependencies. First you will need to change directories to where you want the completed build files to go, in most cases you'll want to create a new directory.

`mkdir ~/{package-name}`

`cd ~/{package-name}`

You then run the command to pull the dependencies from apt-get:

`sudo apt-get build-dep {package-name}`

## Build package from source

Run the command to build the package from source:

`sudo apt-get source --compile {package-name}`

### Note:
If this step fails due to an error with OpenJDK see the page on [Installing Oracle JDK](https://github.com/dnschneid/crouton/wiki/Installing-Oracle-JDK) and then retry.

## Install the package
At this point in time you should see a bunch of *.deb files for the package you want to install and all of the dependencies. Use the following command to install all of them:
`sudo dpkg -iR ~/{package-name}`

### Note:
If you see an error at this point that the dependencies are broken or not met type the following command to fix dependencies:
`sudo apt-get -f install`

Fixing the dependencies should complete the install.

## Footnote:
This should work for most packages.If you run into issues try posting your problem on the [Google+ Crouton Users - Issues and Problems](https://plus.google.com/communities/109120069102230291151/stream/ce07a950-06cb-457e-bf34-de090784cba5) page.