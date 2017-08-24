# Pharo 7 (and 8) potential roadmap

This document contains a list of actions that should be done during Pharo 7 and 8.
It is not a definitive list. It is the result of a discussion within the engineer team and RModers. 
It first lists actions that should be performed at the image level then lists the actions for the VM. 


## Image
As a general principle, we will try to remove something when we add a new features or version of a component. 

### New tools
- Iceberg: Iceberg is the new tool suite to handle new VCS in Pharo. For the moment it supports Git. This tool will become central to the development of projects in Pharo. The key and first enhancements will be: 
  - [DONE] cherry picking
  - multiple directories support, subtrees
  - better new development process support

- Calypso: Calypso is a new tool suite for editing and navigating code. It is modular and can easily be extended. 
  - integration, set as the default browser

- [DONE] Hermes (binary loader): Hermes is a code loader that does not require the compiler to be loaded. It will be used to speed up the bootstrap.

- Beacon: Beacon is an announcement-based logger. It should be introduced but in addition the left over of previous logging should be cleaned and removed for example many of the transcript show: should be removed.

- Inspector refreshing: the inspector should refresh its values by default. 

- Cargo: Cargo is a new package manager. It supports the expression of dependencies between packages. We are currently validating that it can support conditional loading (platform) and building new tooling to express and validate data.

- Check dependencies when committing. Pharo comes with a dependency analyser tools. Such tools should be used before commiting to warn the user when a new dependency is introduced in a package. 

### Cleaning bloat
- Removing of Nautilus: Once Calypso will be integrated and exhibit similar fetaures than Nautilus. Nautilus should be removed.
- Remove old text editor: there is only one or two widgets still using the old text editor and the rubric text editor. 

- Remove Komitter: Iceberg already supports cherrypicking on commit therefore Komitter can be safely removed from Pharo. But versionner should be adapted first because it uses Komitter. 

- Remove system categorizer: The old system categorizer is not used anymore and should be remove. 

- [Marcus should review this] Old compiler removal: The old compiler should now get unloaded from Pharo.
  - The old compiler should be moved to an external package and make sure that it can be reloaded. 
  - [DONE or PR] In addition the encoders should be separated.

- Old inspector c leanup: we should remove the eyeInspector framework but we should introduce a minimal fallback inspector. This inspector should work without Spec or any frameworks. Watch out the SpecDebuggger is using the EyeInspector.

- Clean behavior protocol. The number of methods in the core Behavior, ClassDescription and Class requires some analysis and cleaning. 

- Kernel modularization: the effort on modularising the packages should be continued. 

### Language infrastructure
The following points are more related to the infrastructure of manipulating and loading class definitions. There are the basis for the future module system and cleaning some often hidden parts of the system. 

- New class definition: The class definition is not scaling anymore due to the large number of options (traits, slot, tage, package, immediate, ephemeron). A fluid-based message API should be designed. 

- [Guille ] New code importer infrastructure: the importer should be able to produce MC definitions.

- [Marcus should review this] Support for Undefined classes: When loading old code or code whose superclass has not yet being loaded, the system has inconsistent behavior. Depending on the tools, it may not load the code, raise a warning or decide that the superclass is Object without any other notice and losing the name of the original superclass. We are working on a new mechanism to support a unique way to handle such case. To be presented at IWST/ESUG

-  [Marcus should review this] Class definition AST: Class definition is parser in different place of the system and in addition the output is the direct creation of a class object instead of an object representing the class definition that can be further manipulated. We are working on class definition parser. It produces a separate AST. It will help the building of tools. 

- Better update infrastructure. Pablo Tesone has been working on a better update mechanism, better modular class builder. 

- Ring2: Ring is a metamodel to manipulate code that is not actively running in the current system. It is useful to perform analysis (such as browsing, navigating off-line or remote definitions) before actually loading the code. Ring got redesigned by Pavel Krivanek to support the bootstrap of Pharo 60. The results is Ring2. 

- Opal is the Pharo Compiler. It should be enhanced to support handle better the warning and a general enhancement pass is needed. 

### System enhancements

- Commandline enhancements. RMOD is currently improving the command-line infrastructure and making sure that the system can work in read-only mode.

- Cleaning streams: the idea is to make sure that the system does not use the old streams. The idea is to start using the fileStreams and make sure that the Stream package can be substituated in the future by other streams with the same API. Therefore hardcoded class names should be substituted with factory (readSream writeStream). From that perspective we do not think that it is wise to introduce Xtreams. We should analyse the API of the current implementation. 

- New theme from the beginning. It is really important that each version of Pharo is identified open the default opening. Pharo should come up with two default themes: one light and one dark. 

- Better themes/palettes support

- [Underway] Better and nicer Tabs. Tabs design should be revisited. 

- OSWindow world rendering: the effort to remove the logic of the windowing from the VM should be continued. OSWindow should be used instead. 

- Freetype: The current freetype plugin is the source of many bugs and problem. Thibault Raffaillac used FFI to do direct call to openGL (@@to verify@@).


- Refactoring 2: Refactoring 2 is an improved version of the refactorings developed by Gustavos Santos and they should used to replace the existing one. 


## VM

- 64-bits by default: Mac and Linux 64 bits VMs have been working for over a year and since June 2017 a Windows 64 bits VM has been working. The plan is to stabilize each part of Pharo that is not working in 64 bits as well as in 32 bits and make the 64 bits Pharo images and VM the default download for Pharo.

- Headless VM: Ronie Salgado has built a headless VM on his VM branch, we need to merge and check everything works fine. With the headless VM, Pharo can be run in true headless mode (not with a hidden window) and it is required for future VM features (embeddable VM, ...).

- Embeddable VM: The VM should be able to be embedded in external applications, the application accessing objects through well-defined APIs.

- Idle VM: The goal is to avoid the active waiting loop consuming several per cent of cpu time when the Pharo image is not doing anything.

- Android VM: integration of the Android VM build and tests in the integration setup.

- Threaded FFI: all FFI calls should be non-blocking (reviving prototype of Eliot)

- ZeroConf for ARM: The ARM VM should be available from the zeroConf servers

- Better support for large heaps (GC tuning API, incremental GC). Pharo 64 bit is now able to manage large heap. However better performance can be proposed by offering better settings for the different GC zone.

- Integrate various fixes to support better high resolution display. In Retina mac display Pharo looks blurry. The fix to support Retina should be integrated in the VM.

- Incremental GC. Spur has a generation scavenger that collects garbage in newly created objects (new space), and a mark-compact collector that collects and compacts garbage in old space. Right now on a 2.3GHz MacMini doing normal development, the generation scavenger causes pauses of 1ms or less, and the mark-compact collector than causes pauses of around 200ms.  Both account for about 0.75% of entire execution time (1.5% total), so the scavenger is invoked frequently and the pauses that it creates are not noticeable.  But the occasional pauses created by the mark-compact collector /are/ noticeable, especially in games and music. The idea for the incremental collector is to implement a mark-sweep or mark-sweep-compact collector for old space that works incrementally, doing a little bit of work on each invocation, probably immediately after a scavenge.  It is intended to avoid the long pauses caused by the non-incremental mark-compact collector and make the system more suitable for games, music, etc.



### Sista-related
Sista is an optimizing JIT for Pharo. It is the result of multiple years of developement by Clement Bera and Eliot Miranda. An optimizing JIT is manipulating code (folding constant, rewriting it) before compiling it to assembly code. This is optimizing JIT is a speculative inliner (also known as an adaptive optimizer).  It works by creating special optimized versions of methods that inline smaller methods, thereby allowing optimising away unnecessary tests, sends and blocks, etc.  it is driven by "hot spot" detectors in the first-level JIT code and so inlines code that is better my used a lot.  It is quite similar to the optimisers in Java HotSpot, JavaScript V8, etc.  its implementation and chitecture is more like Graal, because the inliner, optimiser and deoptimiser are all in Smalltalk up in the image.  This in-image optimizer is called Scorch.  The overall architecture is called Sista, for Speculative Inlining Smalltalk Architecture.

- New Block Closure implementation by default: Allows one to implement in the Opal compiler the copying and clean blocks optimisations, reduce massively the complexity of some parts of the JIT (debugging API to map machine code pc to bytecode pc for example) and is required for the Sista support. Some work remains in debugging/IDE support.

- New Bytecode set in production: Eases bytecode decoding (simplifying the code base of the bytecode to source code decompiler, the debugger, the JIT, etc.), lifts compiled code limitations (size of jumps, etc.) and is required for the Sista support. Some work remains in debugging support.

- Read-only objects: They work in Pharo 6, but the in-image support needs to be improved (fall-back code of primitives, etc.) and some polishing needs to be done (primitive error code not always correct, etc.). Used for the support of different project, including GBS.

- Literals immutability (based on read-only objects). In particular an analysis of the image usage of literal (string mutation) is needed.

- Sista preview: A first version of Sista will be integrated, yielding 1.5x performance boost on most applications, but will be optional and will require specific constraints (not toying too much with literal mutability, partial IDE support, etc.).


### Already done

- [DONE] Remove Shoreline reporter.
- [DONE] TxText removal. Remove TxText: TxText was an attempt to get a new generation text editor. Now it has been rewritten with a new design in Bloc so we should remove it since Bloc editor is better and actively maintained.
- [DONE] Kernel should not depend on Traits: This will speed up the bootstrap and support the modular introduction of alternate traits implementation currently designed by Pablo Tesone and tested in the new generation of Moose.

