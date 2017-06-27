# Pharo 7 and 8 potential roadmap

This document contains a list of actions that should be done during Pharo 7 and 8.
It is not a definitive list. It is the result of a discussion within the engineer team and RModers. 
It first lists actions that should be performed at the image level then lists the actions for the VM. 


## Image
As a general principle, we will try to remove something when we add a new features or version of a component. 

- Iceberg: Iceberg is the new tool suite to handle new VCS in Pharo. For the moment it supports Git. This tool will become central to the development of projects in Pharo. The key and first enhancements will be: 
  - cherry picking
  - multiple directories support, subtrees
  - better new development process support

- Calypso: Calypso is a new tool suite for editing and navigating code. It is modular and can easily be extended. 
  - integration, set as the default browser


- Kernel modularization: the effort on modularising the packages should be continued. 
- Hermes (binary loader): Hermes is a code loader that does not require the compiler to be loaded. It will be used to speed up the bootstrap.

- Beacon: Beacon is an announcement-based logger. It should be introduced but in addition the left over of previous logging should be cleaned and removed for example many of the transcript show: should be removed.

### Cleaning bloat
- Removing of Nautilus: Once Calypso will be integrated and exhibit similar fetaures than Nautilus. Nautilus should be removed.
- Remove old text editor: there is only one or two widgets still using the old text editor and the rubric text editor. 
- Remove TxText: TxText was an attempt to get a new generation text editor. Now it has been rewritten with a new design in Bloc so we should remove it since Bloc editor is better and actively maintained.
- Remove Komitter: Iceberg already supports cherrypicking on commit therefore Komitter can be safely removed from Pharo.
- Remove system categorizer: The old system categorizer is not used anymore and should be remove. 

- Old compiler removal: The old compiler should now get unloaded from Pharo.
  - The old compiler should be moved to an external package and make sure that it can be reloaded. 
  - In addition the encoders should be separated. (@@ more here@@)

- Old inspector cleanup: we should remove the eyeInspector framework but we should introduce a minimal fallback inspector. This inspector should work without Spec or any frameworks. 



### Language infrastructure
- Class definition parser
- New class definition
- Support for Undefined class
- Better update infrastructure

- Ring2

- Inspector refreshing: the inspector should refresh its values by default. 

- Versionner with support of baselines
- clean behavior protocol
- pass on Opal


- commandline enhancements



- Cleaning streams: the idea is to make sure that the system do not use the old streams starting 

- New theme from the beginning
- Better themes/palettes support
- Better and nicer Tabs

- Refactoring 2


- OSWindow world rendering
- Cargo
- check dependencies when committing 
- Freetype
### Already done

- [DONE] Remove Shoreline reporter.
- [DONE] Kernel should not depend on Traits: This will speed up the bootstrap and support the modular introduction of alternate traits implementation currently designed by Pablo Tesone and tested in the new generation of Moose.


## VM
- 64-bits by default
- remove display support from VM
- new bloc closures by default (fixes for senders...)
- new bytecode sets in production (method limitations?)
- read only objects
- literals immutability
- Sista preview
- Android VM
- idle VM
- Threaded FFI
- embeddable VM
- ZeroConf for ARM
- large heaps (GC)
- HiRes, Retina?
