### Questions

#### High-level
- Why the Morph / View Split? It seems the Morph is acting like a Presenter; is that right? Is this design decision just to separate concerns into managable chunks since Morphic Morphs are so darn complicated? Usually separating views is more important in systems where the UI is really hard to test, which doesn't apply as much to Smalltalk. I'm wondering if Bloc's Morph should be renamed. Users (like me!) already have a mental model of Morphs from Morphic. For myself, it's very hard to separate the Morph object and the thing I see on the screen. Do you envision hot-swapping views? I ask because the views here feel more like a costume...
- Why split event fetcher and handlers?
- I get moving the event handling logic out of hand, but why not route it back through the hand? Since the hand represents a user, it seems nice and logical that it should symbolically emit the events, even if it delegates the actual work
- What do you think of using the Decorator pattern for e.g. borders, shadows, and scrolling?

#### Class-related
- BlSpaceMorph: Why does it know about the clipboard?
- BlMorph
  - why was `#stepTime` renamed to `#stepPeriod`? Is it a different concept, because otherwise maybe it's not worth it to force users to learn something new...
  - why store the z index as an instance variable at this low level? It seems that few Morphs used this, and it is a property in Morphic

### Understanding
BlUniverse - manages BlSpaces

BlSpace
  - uniquely named
  - [hack]: has a manager for Morphic compatibility, which should eventually be removed
  - has a root morph

BlBlocSpace
  - loop manager - handles the UI process
  - event fetcher
  - event handler

BlSpaceMorph
  - Like a Morphic world, but presents layers, from back to front - world, 

### Experiments

#### Create a Bloc Space to play with (not "the official" way)

```
space := BlBlocSpace new.
canvas := FormCanvas extent: Display extent depth: Display depth.
space rootMorph fullDrawOn: canvas.
canvas
```
