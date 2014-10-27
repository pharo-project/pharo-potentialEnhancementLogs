Here are some notes taken when cleaning Menu. This is work in progress.

Date of last moditication: 30 September 2014
Original author: Stéphane Ducasse
Last modifier: Stéphane Ducasse


## Todo and notes



###enablement selector


enablementSelector: is usually used with a selector or a block
this leads to the following kind of code:

Toggle>>isEnabled
	"Answer whether the item is enabled."
	
	|state|
	self enablementSelector ifNil: [^super isEnabled].
	state := self enablementSelector isSymbol
		ifTrue: [self target perform: self enablementSelector]
		ifFalse: [self enablementSelector value].
	self isEnabled: state.
	^state
	
	
### reducing API of MenuMorph
There are far too many add methods in MenuMorph.
To reduce addToggle and others, one idea could be to use factory

MenuMorph 
	toggleItem
		add: action
		target:...
		
MenuMorph
	updatingItem
	
	
### Removing rarely used methods

- addWithLabel: aLabel enablement: anEnablementSelector action: aSymbol


### Rethinking the item creation API


Instead of having an explosion of combinations
- add: aString target: target selector: aSymbol argumentList: argList
-	add: aString target: target selector: aSymbol argument: arg
-	addToggle: aString target: anObject selector: aSymbol getStateSelector: stateSymbol enablementSelector: enableSymbol argumentList: argList

We could have 
>	aMenuMorph
>		itemLabel: aString;
>		...
>		addItem.
	
or aMenuItem new
	label: aString;
	...
   aMenuMorph addMenuItem: aMenuItem


mainInspectSubMenu: aMenu 
	aMenu 
		add: 'Inspect (i)' translated
		target: self
		selector: #inspectSelectedObjectInNewWindow.
				
	aMenu
		add: 'Explore (I)' translated
		target: self
		selector: #exploreSelectedObject.

mainInspectSubMenu: aMenu 
	aMenu 
		itemLabel: 'Inspect (i)' translated ;
		target: self ;
		selector: #inspectSelectedObjectInNewWindow;
		addItem.
				
	aMenu
		itemLabel: 'Explore (I)' translated ;
		target: self ;
		selector: #exploreSelectedObject;
		addItem
		
mainInspectSubMenu: aMenu 
	aMenu buildItem: [ :item |
		item
			itemLabel: 'Inspect (i)' translated ;
			target: self ;
			selector: #inspectSelectedObjectInNewWindow ].
			

in fact with the block this is super heavy. I understand the idea of esteban: using the structure
of the block to make sure that the right message is sent at the end. But this is really heavy.



### MenuMorph should use submorph but items
Menumorph sometimes use items sometimes submorphs and this is ugly and can led to many bugs.
Menumorph should only use items! (submorphs is an implementation details.)
	
	
This kind of code sucks!

lastItem
	submorphs reverseDo: [ :each |
			(each isKindOf: MenuItemMorph) ifTrue: [ ^each ] ].
	^submorphs last	
	
hasItems
	"Answer if the receiver has menu items"
	^ submorphs
		anySatisfy: [:each | each isKindOf: MenuItemMorph] 	
	
addLine
	"Append a divider line to this menu. Suppress duplicate lines."
	self hasItems
		ifFalse: [^ nil].
	(self lastSubmorph isKindOf: MenuLineMorph)
		ifFalse: [^ self addMorphBack: MenuLineMorph new].
	^ nil	


menuItems should hold all the menuitems and items should hold only the menu
	
### better lineMorph avoiding isKindOf: use
to avoid to have two lines that follows each other the code check their types!

MenuMorph>>addLine
	"Append a divider line to this menu. Suppress duplicate lines."
	self hasItems
		ifFalse: [^ nil].
	(self lastSubmorph isKindOf: MenuLineMorph)
		ifFalse: [^ self addMorphBack: MenuLineMorph new].
	^ nil

It would be better to add a protocol.


MenuMorph>>addLine
	"Append a divider line to this menu. Suppress duplicate lines."
	self hasItems
		ifFalse: [^ nil].
	(self lastItem acceptDuplicates)
		ifTrue: [^ self addMorphBack: MenuLineMorph new].
	^ nil


### Title management is a hack
The first step would be to put an instance variable titleString in MenuMorph 
and revisit such kind of code.

MenuMorph>>updateColor
	"Update the color of the menu."

	self theme preferGradientFill
		ifFalse: [ ^ self ].
	self fillStyle: (self theme menuFillStyleFor: self).	
	"update the title color"
	self allMorphs
		detect: [ :each | each hasProperty: #titleString ]
		ifFound: [ :title | title fillStyle: (self theme menuTitleFillStyleFor: title) ]

MenuMorph>>informUserAt: aPoint during: aBlock 
	"Add this menu to the Morphic world during the execution of the given
	block. "
	| title w |
	title := self allMorphs
				detect: [:ea | ea hasProperty: #titleString].
	title := title submorphs first.
	self visible: false.
	w := ActiveWorld.
	aBlock
		value: [:string | 
			self visible
				ifFalse: [w addMorph: self centeredNear: aPoint.
					self visible: true].
			title contents: string.
			self setConstrainedPosition: self activeHand cursorPoint hangOut: false.
			self changed.
			w displayWorld
			"show myself"].
	self delete.
	w displayWorld	
		
MenuMorph>>addTitle: aString icon: aForm
	"Add a title line at the top of this menu."

	| title titleContainer |
	title := AlignmentMorph newColumn.
	self setTitleParametersFor: title.	""
	aForm isNil
		ifTrue: [ titleContainer := title ]
		ifFalse: [ 
			| pair |
			pair := AlignmentMorph newRow.
			pair color: Color transparent.
			pair hResizing: #shrinkWrap.
			pair layoutInset: 0.	""
			pair addMorphBack: aForm asMorph.	""
			titleContainer := AlignmentMorph newColumn.
			titleContainer color: Color transparent.
			titleContainer vResizing: #shrinkWrap.
			titleContainer wrapCentering: #center.
			titleContainer cellPositioning: #topCenter.
			titleContainer layoutInset: 0.
			pair addMorphBack: titleContainer.	""
			title addMorphBack: pair ].	""

		aString asString
				linesDo: [ :line | titleContainer addMorphBack: (StringMorph contents: line font: StandardFonts menuFont) ].
		
	title setProperty: #titleString toValue: aString.
	self addMorphFront: title.
	title useSquareCorners.
	title on: #mouseDown send: #mouseDownInTitle: to: self.
	(self hasProperty: #needsTitlebarWidgets)
		ifTrue: [ self addStayUpIcons ]	
		
		
MenuMorph>>addStayUpIcons
	"Add the titlebar with buttons."
	
	|title closeBox pinBox titleBarArea titleString spacer1 spacer2|
	title := submorphs
		detect: [:ea | ea hasProperty: #titleString]
		ifNone: [self setProperty: #needsTitlebarWidgets toValue: true.
				^self].
	closeBox := IconicButton new target: self;
		actionSelector: #delete;
		labelGraphic: self theme menuCloseForm;
		color: Color transparent;
		extent: 18 @ 18;
		borderWidth: 0.
	pinBox := IconicButton new target: self;
		actionSelector: #stayUp:;
		arguments: {true};
		labelGraphic: self theme menuPinForm;
		color: Color transparent;
		extent: 18 @ 18;
		borderWidth: 0.
	closeBox setBalloonText: 'Close this menu' translated.
	pinBox setBalloonText: 'Keep this menu up' translated.
	spacer1 := AlignmentMorph newSpacer: Color transparent.
	spacer1 width: 14;
		 hResizing: #rigid.
	spacer2 := AlignmentMorph newSpacer: Color transparent.
	spacer2 width: 14;
		 hResizing: #rigid.
	titleBarArea := AlignmentMorph newRow vResizing: #shrinkWrap;
		layoutInset: 2;
		color: title color;
		addMorphBack: closeBox;
		addMorphBack: spacer1;
		addMorphBack: title;
		addMorphBack: spacer2;
		addMorphBack: pinBox.
	title color: Color transparent.
	titleString := title
		findDeepSubmorphThat: [:each | each respondsTo: #font:]
		ifAbsent: [].
	titleString font: StandardFonts windowTitleFont.
	self theme currentSettings preferRoundCorner ifTrue: [
		titleBarArea
			roundedCorners: #(1 4);
			useRoundedCorners].
	self addMorphFront: titleBarArea.
	titleBarArea
		setProperty: #titleString
		toValue: (title valueOfProperty: #titleString).
	title removeProperty: #titleString.
	self setProperty: #hasTitlebarWidgets toValue: true.
	self removeProperty: #needsTitlebarWidgets.
	self removeStayUpItems		
	

### we deprecated add: aString target: aTarget action: aSymbol
we deprecated add: aString target: aTarget action: aSymbol
but add:action: is still used!
and we still have add:selector:....


### creating ToggleMenuItem
We are only creating ToggleMenuItem so we should merge the class with its superclass.


### should check addList: and fromArray: 
-- fromArray: is far from an exciting class creation message.
-- addList: has a variable addTranslatedList: that is not used. We should merge them and fix all the senders.



## Done

### MenuMorph>>title: and MenuMorph>>addTitle:

[Done in 14122]
MenuMorph>>title: is just a call to addTitle: and it is blurry because window defines also title:
So I suggest to deprecate the title: method and keep addTitle:. addTitle: is better because it really suggests what it does: adding a title because we can have menu without title.


### PluggableMenuSpec and MenuSpec

-- DONE in 14117 
	MenuSpec looks like hopeless with simply two attributes.
	PluggableMenuSpec is not a subclass of menuSpec.
	We should merge MenuSpec with its subclass PluggableMenuItemSpec.

-- DONE rename Pluggable into Plain
	PluggableMenuSpec into MenuSpec
	PluggbaleMenuItemSpec into MenuItemSpec
	
###translated should be handled inside the add:....
instead of 


mainInspectSubMenu: aMenu 
	aMenu 
		add: 'Inspect (i)' translated
		target: self
		selector: #inspectSelectedObjectInNewWindow.
				
	aMenu
		add: 'Explore (I)' translated
		target: self
		selector: #exploreSelectedObject.

the translated method should be somewhere inside the add:target..... method.





apparently in 
addToggle: aString target: anObject selector: aSymbol getStateSelector: stateSymbol enablementSelector: enableSymbol argumentList: argList

addToggle: aString target: anObject selector: aSymbol getStateSelector: stateSymbol enablementSelector: enableSymbol argumentList: argList
	"Append a menu item with the given label. If the item is selected, it will send the given selector to the target object."

	|item|
	item := ToggleMenuItemMorph new
		contents: aString translated;
		target: anObject;
		selector: aSymbol;
		arguments: argList;
		getStateSelector: stateSymbol;
		enablementSelector: enableSymbol.
	^ self addMenuItem: item.

