This is version v1 24 August 2014 of the roadmap.

Source: [Nicolai Hess: Refactor Nautilus](http://forum.world.st/Refactor-Nautilus-td4769767.html)

### Objective 

The objective is to refactor and cleanup Nautilus code.

#### This includes:

* renaming / recategorizing
* solve code critics shown in Critics Browser
* split classes (AbstractNautilusUI has  427 methods 24 instvars )
* stronger separation between the browser model, browser state and browser UI.
  * review event handling and UI updating (sometimes there is a difference between what the UI shows as selected and the selection the model(s) holds

### Maybe (something to consider)

 * create one package pane widget for package and groups (therefore move all that package vs groups handling from nautilus ui to that widget)
 * merge Nautilus and PackageTreeNautilus

Nicolai: I don't consider all of Nautilus bad code, it has well
designed parts, it just has grown meanwhile...

### How to update the code?

Working with slices makes it easier to find changes  that introduces new bugs. Otherwise it takes time to do it in such small steps and I don't know if those changes are reviewed at all.

So, we would try to group the changes.
1. code critics
2. renaming and simple refactoring
3. ....

### Other suggestions

Source: [Roadmaps on tools?](http://forum.world.st/Roadmap-on-tools-td4774285.html)

These things likely do not belong to the category of code cleaning and refactoring. But we want to keep it in mind.

 * I would remove the bytecode and text icons (which are not consistent with the instance access BTW)
 * I would remove the history navigation for example 
 * We will certainly add (but in a modular way) the GT-Inspector. 

### Questions

 * Should we integrate [Rubric](http://www.smalltalkhub.com/#!/~AlainPlantec/Rubric)?