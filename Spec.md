Here are some notes for the simplification of Spec
---------------------------------------------------

### exposing more ports

We often have 

Model>>whenImageChanged: aBlock
	<api: #event>
	"Set a block to performed when the image is changed"
	
	imageHolder whenChangedDo: aBlock

the client 
	
	aModel whenImageChanged: []


could be simplified by letting the clients sending whenChangedDo: to the holder 	

		aModel imageHolder whenChangedDo: []
		
imageHolder is a public property so we can also use imageHolder value.




### whenChangedSend:to:with:
We should also avoid block argument and we should introduce whenChangedSend:to:with: in Announcer

whenChangedSend: aSelector to: aReceiver

	announcer when: ValueChanged send: aSelector to: aReceiver
	

### Sharing announcers
Each valueHolder as its own announcer. 
ValueHolder could share their announcers with their composableModel for example. 


Here are the notes of Henrik.


ListModel, <insert relevant explicits here>!
> There are, count them and weep, *32* different NewValueModels instance variables, each with different announcers.
>
> In the case I was looking at, in addition to a listHolder inst var (a CollectionValueHolder), there's a listAnnouncer inst var (an Announcer), so just finding the one through which announcements are made is next to impossible (with the ListModel's own announcer, that's three different ones that would potentially make sense), at least without consulting the *offical* API; #whenListChanged:
>
> Turns out, in the current implementation, listAnnouncer is the one I want.
> Which isn't exposed through an accessor, so you can't even use it to subscribe directly. Yay.
> </rant>
>
> If someone has time to restructure this, I would like to suggest changing the internals radically;
> - Introduce new Announcement types for all the different Value holders that make sense (SelectionChanged, ListChanged), and drop the rest. I mean, whenSortingBlockChanged: ?? Who on earth would be interested in that, and not already respond to ListChanged (or it's current equivalent)
> - Reduce the amount of announcers to a saner number, say, 1 (or, in a pinch, 2).

Yeah there are "Announcement storms" going on with Spec based tools. I think that it explains a lot of the sluggishness of some tools, like Nautilus.

I saw these when doing some Nautilus plug-ins for my own use (e.g. bring back the original senders/implementors... buttons).

Squeak tools feel very very snappy in comparison. Even on a superfast PC, Pharo as some annoying delays.

Announcements are great but overusing them is receipe for serious head scratching and does not lend itself to easy discoverability of how things do work. 