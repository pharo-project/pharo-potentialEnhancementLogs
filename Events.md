Here is a draft analysis of the possible actions to be done to continue the work on the integration of OSWindow into Pharo. These notes were taken during a discussion between S. Ducasse, I. Stasenko and E. Lorenzano. They can be wrong and should not be taken as granted. 

Previous to OSWindow
--------------------------------------------------------
HandMorph>>processEvents was handling events
	from Sensor which takes them inputEventSensor


In OSWindow handleEvent: anEvent
-------------------------------------------------------------------
       OSWindowMorphicEventHandler>>dispatchMorphicEvent: is dispatching.

--------------------------
We are in migration period.

We should move the logic of the InputEventFetcher to the OSVMWindowDriver (represents the old system
within the OSWindow frameworks).


OSWindowMorphicEventHandler>>dispatchMorphicEvent: anEvent 
	should be cleaned.

OSWindowMorphicEventHandler>>dispatchMorphicEvent: anEvent 

	morphicWorld defer: [ morphicWorld activeHand handleEvent: anEvent ]. 

This method should rewritten with a more efficient approach. We should remove the use of defer:
The OSWindowMorphicEventHandler should act similar to the input Event Fetcher process
by queueing the incoming events so that later will be processed by the UI process (via the hand)
The hand or similar object should fetch events. 

We should pay attention that when then there is no UI process we should not explode. 
Probably the UI Process should be responsible to set the fetching process.


VMDriver should use
- input semaphore
- fetch event
- convert of to OSWindowEvent	
- dispatch to the window's handler

The three first steps are in Sensor logic.

We should rewrite the HandMorph to act as a windowHandler

----------------------------------------------------------------------------------------------

OSSDL2Driver>>processEvent: sdlEvent
	| event |
	event := self convertEvent: sdlEvent.
	event ifNotNil: [ eventQueue nextPut: event ].

OSSDL2Driver>>processEvent: sdlEvent should not have a queue, or the queue in the window.
It should call a OSWindow.


-----------------------------------------------------------------------------------------------
the logic should be

World
	startUp
		createWindow
			setup event processing. The event handler should be the responsibility of OSSystemWindow
			

Each OSWindow has the event handler as shown below.
		
recreateOSWindow
	| attributes driver |
	session := Smalltalk session.
	attributes := OSWindowAttributes new.
	attributes extent: self extent;
		title: Smalltalk shortImageName;
		icon: Smalltalk ui icons pharoIcon.
		
	driver := self pickMostSuitableWindowDriver.
	attributes preferableDriver: driver.
	osWindow := OSWindow createWithAttributes: attributes.
	osWindow newFormRenderer: Display.
	osWindow eventHandler: (OSWindowMorphicEventHandler for: self).		


The Driver knows how to create the window. But it does not know when. 
The client sets up the handler. We can have custom handler for any window.
		

------------------------------------------------------------------------------------------------
After the migration period
The Window driver should be responsible to deliver event to consumer similarly 

OSSDL2Driver>>eventLoopProcessWithPlugin
	| event session semaIndex |
	event := SDL_Event new.
	session := Smalltalk session.
	
	semaIndex := (Smalltalk registerExternalObject: inputSemaphore).
	self primSetupInputSemaphore: semaIndex.
	[
		[ inputSemaphore wait; consumeAllSignals. session  == Smalltalk session] whileTrue: [
			[ (SDL2 pollEvent: event) > 0 ] whileTrue: [
				self processEvent: event
			].
		]
	] ensure:  [ Smalltalk unregisterExternalObject: inputSemaphore ]


------------------------------------------------------------------------------------------------
We can clean and remove all the calling tree calling dispatchMorphicEvent: 

We should probably remove OSSDL2Driver>>eventLoopProcessWithoutPlugin

--------------------------------------------------------------------------------------------------

We should take advantage about the presence of SDL to bypass the bitblt logic for the rendering.

