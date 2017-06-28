# Pharo 7 (and 8) potential roadmap

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

- Clean behavior protocol. The number of methods in the core Behavior, ClassDescription and Class requires some analysis and cleaning. 

### Language infrastructure

- Class definition parser: 

- New class definition: The class definition is not scaling anymore due to the large number of options (traits, slo, tage, package, immediate, ephemeron). A fluid-based message API should be designed. 

- Support for Undefined class: When loading old code or code whose superclass has not yet being loaded, the system has inconsistent behavior. Depending on the tools, it may not load the code, raise a warning or decide that the superclass is Object without any other notice and losing the name of the original superclass. We are working on a new mechanism to support a unique way to handle such case. To be presented at IWST/ESUG

- Better update infrastructure

- Ring2

- Inspector refreshing: the inspector should refresh its values by default. 

- Cargo: Cargo is a new package manager. It supports the expression of dependencies between packages. We are currently validating that it can support conditional loading (platform) and building new tooling to express and validate data. 
- Check dependencies when committing 
- Versionner should be updated to support of baselines. 

- pass on Opal


- Commandline enhancements. RMOD is currently improving the command-line infrastructure and making sure that the system can work in read-only mode.



- Cleaning streams: the idea is to make sure that the system does not use the old streams. The idea is to start using the fileStreams and make sure that the Stream package can be substituated in the future by other streams with the same API. Therefore hardcoded class names should be substituted with factory (readSream writeStream). From that perspective we do not think that it is wise to introduce Xtreams. We should analyse the API of the current implementation. 

- New theme from the beginning
- Better themes/palettes support
- Better and nicer Tabs
- OSWindow world rendering: the effort to remove the logic of the windowing from the VM should be continued. OSWindow should be used instead. 

- Refactoring 2: Refactoring 2 is an improved version of the refactorings.




- Freetype: The current freetype plugin is the source of many bugs and problem. Thibault Raffaillac used FFI to do direct call to openGL (@@to verify@@).

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
