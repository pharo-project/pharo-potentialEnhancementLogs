## GNU/Linux

### Problems

- the method for different platforms leaves you with different products (e.g. Ubuntu -> Launcher, but Debian -> VM-only)
- the instructions at http://pharo.org/download are incomplete (e.g. Debian step 2.i.a below)

### Questions
- Can we pick up the debian artifact from the [Debian build](https://swing.fit.cvut.cz/jenkins/view/Projects/job/pharo-vm-stable-swing/) and create a launcher on our CI with it?

### Suggestions (for http://pharo.org/download)
- Remove all GNU/Linux instructions from the page. Instead, when a user clicks on the Linux button, they should be taken to a whole different page that explains the intricacies of each platform.
- On the page described above:
  - put a clear heading for each GNU/Linux flavor (e.g. `###Debian` instead of "Looking for a debian distribution?")
  - put a catch-all for Other, with a way to get in contact with us if one runs into trouble that does not require login or subscription (i.e. not the mailing list or fogbugz). IMO an email address would be perfect
- [OT]: Change all references to `Linux` -> `GNU/Linux`. See https://www.gnu.org/gnu/gnu-linux-faq.html#why

### Flavors

#### Ubuntu
- Follow PPA instructions
- Link to the [Launcher tutorial](https://github.com/SquareBracketAssociates/PharoInProgress/tree/master/PharoLauncherTutorial) once there is a stable artifact

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

#### Other distributions?
