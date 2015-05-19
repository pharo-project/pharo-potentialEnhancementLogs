Discussion started at http://forum.world.st/Tx-Pass-tt4822173.html :
Just like Bloc (which has *great* class comments), I think it's crucial to document the high level design decisions in Tx. I did a first pass and noted some initial impressions...

### Questions

##### TxModel
Overall, it seems to have waaaay too much responsibility:
- know my start and end span
- provide a stream of characters
- know all my styles
- provide span access and enumeration
- create selections
- provide positions
- create cursors
- organize 

####Design Questions
- TxModel
  - what is the advantage of a double linked list vs the old Text implementation? (Pull answer from ml)
  - `#cursor` to me is misleading; it sounds like there is a single cursor, like in a text editor. In fact, I'd like to remove all cursor and selection-related behavior out of TxModel
  - Span protocols seem a little disjoined. There is `#spans`, which returns an array; `#spansDo:`, `#startSpan`, and `#lastSpan`. In fact, why is it anyone's business that we hold spans? What does a client do with this info? Maybe this all should be private
  - `#asStringOn:`. We have `#characterStream`. Why not use it instead of re-implementing the logic? E.g.
```
charStream := self characterStream.
String streamContents: [ :str |
        [ self isAtEnd ] whileFalse: [ charStream nextPut: self next ] ]
```
- `TxCharacterStream` does not implement full `Stream` protocol e.g. `#nextPut:`
- TxLayoutViewMorph vs. TxTextEditorMorph - I'm not clear on the purpose/relationship/pattern here?
- TxForeColorAttribute - Why not TxFontColorAttribute? Is the fore/font distinction important?
- TxXyzAttribute - can we remove the 'Attribute' from all but the base class? e.g. TxFontAttribute -> TxFont
- TxModel class comment - "I don't provide a direct interface for mutating/editing my data (and this is a very important point). Instead I am modified using position(s) (TxTextPosition) and/or selection(s) (TxInterval/TxSelection), providing a rich protocol for various operations over text." That sounds intriguing, but why is that so significant?
[Per camille teruel](http://forum.world.st/TxText-More-Cleaning-and-Questions-tp4823894p4824197.html):

> since you want that every operation takes constant time you cannot provide operations that take indexes.
Instead you use positions/cursors and intervals/selections because they know to which spans they correspond.

- TxStyle - why both #at: and #get:?
- `TxBasicTextCursor subclass: TxTextCursor` hasA: `TxBasicTextCursor subclass: TxTextPosition`. Huh?
- TxModel
  - Why #characterStream?
  - Why cursor? At best, a poor name since it doesn't return "the cursor for the text", but a representation of a cursor
- TxActionAttribute should probably implement class-side `#filter:value:`. `TxActionAttribute new filter: aBlock; value: aBlock; yourself` seems to be a common usage.

####Implementation Questions:
- TxBasicSpan#< seems overly complex. Why two-way searching. Was it the result of profiling? Intuition? Random?
- TxTextEditorMorph>>#openInWindowWithText: says not part of API, but this code is pretty much the only way to use it for now, right?
- applyAttribute:to: sends an unimplemented message, but I'm not sure what should be done here
- Why no strike fonts? Why is Verdana commented out in TxFontAttribute class>>#defaultValue?
- TxLayoutViewMorph class>>#text: sends #asTxModel to argument, but TxTextEditorMorph doesn't. Which one is right? I think there's not much harm in sending it in both to make it a bit easier for users...
- TxBasicViewMorph>>#fullDrawOnAthensCanvas: checks if fullBounds are visible on canvas, then checks #bounds again. Are both necessary? 

###Ideas
- Since TxSelection is immutable, how about TxEmptySelection or TxNoSelection
