Discussion started at http://forum.world.st/Tx-Pass-tt4822173.html :
Just like Bloc (which has *great* class comments), I think it's crucial to document the high level design decisions in Tx. I did a first pass and noted some initial impressions...

### Questions
####Design Questions
- TxModel - what is the advantage of a double linked list vs the old Text implementation?
- TxLayoutViewMorph vs. TxTextEditorMorph - I'm not clear on the purpose/relationship/pattern here?
- TxForeColorAttribute - Why not TxFontColorAttribute? Is the fore/font distinction important?
- TxXyzAttribute - can we remove the 'Attribute' from all but the base class? e.g. TxFontAttribute -> TxFont
- TxModel class comment - "I don't provide a direct interface for mutating/editing my data (and this is a very important point). Instead I am modified using position(s) (TxTextPosition) and/or selection(s) (TxInterval/TxSelection), providing a rich protocol for various operations over text." That sounds intriguing, but why is that so significant?
- TxStyle - why both #at: and #get:?

####Implementation Questions:
- TxBasicSpan#< seems overly complex. Why two-way searching. Was it the result of profiling? Intuition? Random?
- TxTextEditorMorph>>#openInWindowWithText: says not part of API, but this code is pretty much the only way to use it for now, right?
- applyAttribute:to: sends an unimplemented message, but I'm not sure what should be done here
- Why no strike fonts? Why is Verdana commented out in TxFontAttribute class>>#defaultValue?
- TxLayoutViewMorph class>>#text: sends #asTxModel to argument, but TxTextEditorMorph doesn't. Which one is right? I think there's not much harm in sending it in both to make it a bit easier for users...
- TxBasicViewMorph>>#fullDrawOnAthensCanvas: checks if fullBounds are visible on canvas, then checks #bounds again. Are both necessary? 

###Ideas
- Since TxSelection is immutable, how about TxEmptySelection or TxNoSelection
