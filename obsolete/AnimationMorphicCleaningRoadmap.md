Hi,

Now with holidays coming? maybe someone wants to do a small Pharo related Project with impact on the future?

Here is suggestion 1. 

Context: cleanup graphics. (so e.g. one could replace the canvas later).

Details
======
	Morphic has some very arcane code for animating moving morphs around that bypasses layers.

DisplayObject has these methods:

follow:while:
follow:while:bitsBehind:startingLoc:
slideFrom:to:nSteps:delay:
slideWithFirstFrom:to:nSteps:delay:
displayOnPort:at:rule: 

They are used by morphic for some not really important animation that for sure
can be directly done by moving the morphs as morphs around. Bypassing
and directly using low level (not even Canvas level) functionality is for sure not needed.

On the Morph level the users are:

slideBackToFormerSituation:
vanishAfterSlidingTo:event:
animationForMoveSuccess:

What to do?

-> rewrite the morphic level code to just move the morphs around.
-> delete all the code from DisplayObject
-> review ?GrafPort? usage, as this might have been the last client.


	Marcus






