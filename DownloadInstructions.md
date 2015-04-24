## GNU/Linux

### TODO
- put a catch-all for Other, with a way to get in contact with us if one runs into trouble that does not require login or subscription (i.e. not the mailing list or fogbugz). IMO an email address would be perfect
- Link to the [Launcher tutorial](https://github.com/SquareBracketAssociates/PharoInProgress/tree/master/PharoLauncherTutorial). Right now, there are multiple formats there but probably it would be better to mirror to http://files.pharo.org
- Can we make a deb from the PPA? See https://wiki.debian.org/CreatePackageFromPPA
- The file server seems messy- http://files.pharo.org/vm/pharo/linux/debian_ubuntu/ - There is a deb package from 2013, and a README saying to try the PPA for Ubuntu, but the folder should just be renamed to Debian, no? Or maybe remove entirely. What is this actually installing? Which Pharo version? 

#### 64-bit Systems

###### Other
Worked on Mint, doesn't work in Ubuntu 14.0.4 (from http://blog.bit-man.guru/2014/09/how-to-run-pharo-30-on-top-of-linux-64.html)
```
  sudo aptitude install lib32z1 lib32ncurses5 lib32bz2-1.0
  sudo aptitude install libx11-6:i386
  sudo aptitude install libgl1-mesa-glx:i386
```

#### External Resources
https://launchpad.net/~pharo/+archive/ubuntu/stable
https://www.ruby-lang.org/en/documentation/installation/

## Solved
- the method for different platforms leaves you with different products (e.g. Ubuntu -> Launcher, but Debian -> VM-only). [SOLVED]: The downloads were standardized and categorized - default is vm+image.zip for all platforms, other options (e.g. launcher or vm-only) were made explicit
- the instructions at http://pharo.org/download are incomplete (e.g. Debian step 2.i.a below)[SOLVED]: We gave the website GNU/Linux instructions a massive overhaul. See http://pharo.org/gnu-linux-installation
- Old Lib C downloads out of date: http://files.pharo.org/vm/pharo/linux/old-libc/ last pharovm-ubuntu804.tar.gz is from 8/28/2013, but most recent swing buld is from 9/2014.[SOLVED]: We now have our own libc < 2.15 CI build, with artifacts mirrored to files.pharo.org, and linked from the website
- Can we pick up the debian artifact from the [Debian build](https://swing.fit.cvut.cz/jenkins/view/Projects/job/pharo-vm-stable-swing/) and create a launcher on our CI with it? [SOLVED]: We now have our own libc < 2.15 CI build, with artifacts mirrored to files.pharo.org, and linked from the website
- [SOLVED]: Link Launcher instructions to the [Launcher tutorial](https://github.com/SquareBracketAssociates/PharoInProgress/tree/master/PharoLauncherTutorial).
- [SOLVED]: Remove all GNU/Linux instructions from the main download page. Instead, when a user clicks on the Linux button, they should be taken to a whole different page that explains the intricacies of each platform.
- [SOLVED]: On the page described above:
  - [SOLVED]: put a clear heading for each GNU/Linux flavor (e.g. `###Debian` instead of "Looking for a debian distribution?")
- [SOLVED]: [OT]: Change all references to `Linux` -> `GNU/Linux`. See https://www.gnu.org/gnu/gnu-linux-faq.html#why
