 Here are some notes for the simplification of Spec

# Inconsistent instance variables naming

It is really confusing that some variables are named *Holder while other not. So you never know and only the initialize method can tell us.



#ifNotNil plague

There are far too many tests checking for nil after a value is executed. This is because the valueHolder are not initialized correctly. For example in the ComposableModel>>initialize method below

initialize

	super initialize.

	extentHolder := nil asValueHolder.
	needRebuild := true asValueHolder.
	keyStrokesForNextFocusHolder := { KMNoShortcut new } asValueHolder.
	keyStrokesForPreviousFocusHolder := { KMNoShortcut new } asValueHolder.
	additionalKeyBindings := Dictionary new.
	announcer := Announcer new asValueHolder.
	aboutText := nil asValueHolder.
	windowIcon := nil asValueHolder.
	window := nil asValueHolder.
	askOkToClose := false asValueHolder.
	titleHolder := self class title asValueHolder.


aboutText

	^ aboutText value ifNil: [ aboutText value: self class comment ]
	
=> 

initialize

	super initialize.
	extentHolder := nil asValueHolder.
	needRebuild := true asValueHolder.
	keyStrokesForNextFocusHolder := { KMNoShortcut new } asValueHolder.
	keyStrokesForPreviousFocusHolder := { KMNoShortcut new } asValueHolder.
	additionalKeyBindings := Dictionary new.
	announcer := Announcer new asValueHolder.
	aboutText := self class comment asValueHolder.
	windowIcon := nil asValueHolder.
	window := nil asValueHolder.
	askOkToClose := false asValueHolder.
	titleHolder := self class title asValueHolder.

We should remove aboutText but this is a nice example of bad use of nil as initial value holder value.


# rethinking default
Another example that could be fixed but require some thought is

initialize
	super initialize.
	extentHolder := nil asValueHolder.

should be 

initialize
	super initialize.
	extentHolder := self initialExtent asValueHolder.
	^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

initialExtent

	^ 200@500

Now in fact is doing a kind of lookup: first it gives precedence to the model then to the widget when the model does not provide an extent and this is something that we are somehow breaking by imposing that a model has a default. 

MorphicAdapter>>heightToDisplayInList: aList
	"Return the width of my representation as a list item"
	
	self model extent ifNotNil: [:ex | ^ ex y ].
	self model initialExtent ifNotNil: [:ex | ^ ex y ].

	self widget ifNil: [ self buildWithSpec ].
	self widget 
		vResizing: #rigid;
		hResizing: #rigid.
		
	^ self widget heightToDisplayInList: aList


# The case of borderWidth and borderColor:
AbstractWidget>>borderWidth: 

AbstractWidget>>borderColor: 

do not seem to be used, could make sense on ButtonModel but it does not work.

ButtonModel new 
	label: 'blbl';
	borderWidth: 100;
	borderColor: Color red;
	openWithSpec
	
does not work

Solution:
- remove borderWidth: from the superclass
- move it to ButtonModel
- make it work


# Block Plague
We often have 

Model>>whenImageChanged: aBlock
	<api: #event>
	"Set a block to performed when the image is changed"
	
	imageHolder whenChangedDo: aBlock

the client 
	
	aModel whenImageChanged: []


could be simplified by letting the clients sending whenChangedDo: to the holder 	

		aModel imageHolder whenChangedDo: []
		
imageHolder is a public property so we can also use imageHolder value


# extent of widget
It would be good to set as invariant that a widget should know its extent. This would avoid this ugly responsTo: messages.

ComposableModel>>ensureExtentFor: widget

	self extent
		ifNil: [ self initialExtent
			ifNotNil: [ :ex | 
				(widget respondsTo: #extent:)
					ifTrue: [ widget extent: ex ] ] ]
		ifNotNil: [ :ex | 
			(widget respondsTo: #extent:)
				ifTrue: [ widget extent: ex ] ].


# methods to be removed 
- whenHelpChanged: aBlock 
- whenBorderWidthChanged: aBlock
- whenBorderColorChanged: aBlock
- 

# case for 

ButtonModel>>whenActionPerformedDo: aBlock
	<api: #event>
	"set a block to perform after that the button has been aclicked, and its action performed"

	actionPerformedHolder whenChangedDo: aBlock

Here the actionPerformedHolder is just another holder to be able to fire an extra action in addition 
to others. 



# ComposableModel>>on: anAnnouncement send: aSelector to: aTarget

	self announcer
		when: anAnnouncement 
		send: aSelector 
		to: aTarget
		
should be probably renamed when:send:to: to be coherent with announcement API.		
