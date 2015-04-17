## GNU/Linux

### Problems

- the method for different platforms leaves you with different products (e.g. Ubuntu -> Launcher, but Debian -> VM-only)
- the instructions at http://pharo.org/download are incomplete (e.g. Debian step 2.i.a below)
- Old Lib C downloads out of date: http://files.pharo.org/vm/pharo/linux/old-libc/ last pharovm-ubuntu804.tar.gz is from 8/28/2013, but most recent swing buld is from 9/2014.
- http://files.pharo.org/vm/pharo/linux/debian_ubuntu/ - There is a deb package from 2013, and a README saying to try the PPA for Ubuntu, but the folder should just be renamed to Debian, no? Or maybe remove entirely. What is this actually installing? Which Pharo version? 

### Questions
- Can we pick up the debian artifact from the [Debian build](https://swing.fit.cvut.cz/jenkins/view/Projects/job/pharo-vm-stable-swing/) and create a launcher on our CI with it?

### Suggestions (for http://pharo.org/download)
  - put a catch-all for Other, with a way to get in contact with us if one runs into trouble that does not require login or subscription (i.e. not the mailing list or fogbugz). IMO an email address would be perfect

Done:
- Remove all GNU/Linux instructions from the page. Instead, when a user clicks on the Linux button, they should be taken to a whole different page that explains the intricacies of each platform.
- On the page described above:
  - put a clear heading for each GNU/Linux flavor (e.g. `###Debian` instead of "Looking for a debian distribution?")
- [OT]: Change all references to `Linux` -> `GNU/Linux`. See https://www.gnu.org/gnu/gnu-linux-faq.html#why


### Flavors

#### Ubuntu *** For Pharo 5 when we convert everything to the Pharo Launcher
- Follow PPA instructions
- Link to the [Launcher tutorial](https://github.com/SquareBracketAssociates/PharoInProgress/tree/master/PharoLauncherTutorial). Right now, there are multiple formats at https://ci.inria.fr/pharo-contribution/job/PharoBookWorkInProgress/lastSuccessfulBuild/artifact/PharoLauncherTutorial/ but it would be better to mirror to http://files.pharo.org

#### Debian (jessie and later)
Can we make a deb from the PPA? See https://wiki.debian.org/CreatePackageFromPPA

#### Distributions with glibc < 2.15
1.    Install pre-compiled binary from the [Debian build](https://swing.fit.cvut.cz/jenkins/view/Projects/job/pharo-vm-stable-swing/)
2.    Install 32-bit libs
  1. Enable 32 bit package installation (http://serverfault.com/a/424484)
    1. sudo dpkg --add-architecture i386
    2. sudo apt-get update
  2. sudo aptitude install ia32-libs
3.    Get an image - we only have the VM so far!    

#### 64-bit Systems

Pull from http://forum.world.st/Urgent-Clean-up-Download-Instructions-before-4-0-Release-tp4818938p4819428.html

##### If no ia32-libs:
###### Ubuntu 14.04
workaround - install pharo-vm-core from ppa
###### Other
Worked on Mint, doesn't work in Ubuntu 14.0.4 (from http://blog.bit-man.guru/2014/09/how-to-run-pharo-30-on-top-of-linux-64.html)
```
  sudo aptitude install lib32z1 lib32ncurses5 lib32bz2-1.0
  sudo aptitude install libx11-6:i386
  sudo aptitude install libgl1-mesa-glx:i386
```

#### Other distributions?

#### Random Snippets
https://launchpad.net/~pharo/+archive/ubuntu/stable
https://www.ruby-lang.org/en/documentation/installation/

! PharoLauncher - Our GUI Dashboard
If you're new to Pharo, the easiest place to start is with our GUI Dashboard, PharoLauncher.