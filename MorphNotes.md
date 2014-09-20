##Morphic Notes

Here are some notes about various morph aspects. Not all of them should be cleaned. Still this is interesting to be aware of some.
Author: Stéphane Ducasse
Date of Latest Modification: 20 September 2014

###UITheme and Widgets Analysis

interesting resources: 
http://www.humane-assessment.com/blog/a-pharo-refactoring-story-adding-theme-ability-to-a-morph


#### first the factory API should be pushed in the respective classes


For example

UItheme>>newButtonIn: aThemedMorph for: aModel getState: stateSel action: actionSel arguments: args getEnabled: enabledSel getLabel: labelSel help: helpText
	"Answer a new button."

	|b|
	b := PluggableButtonMorph
		on: aModel
		getState: stateSel
		action: actionSel
		label: labelSel.
		b
			theme: self;
			label: ' ' font: self buttonFont;
			update: labelSel;
			arguments: (args ifNil: [{b}]);
			getEnabledSelector: enabledSel;
			cornerStyle: (self buttonCornerStyleIn: aThemedMorph);
			hResizing: #shrinkWrap;
			vResizing: #shrinkWrap;
			setBalloonText: helpText;
			extent: b minExtent;
			removeProperty: #theme.
			^b

should be transformed into 

PluggableButtonMorph>>newButtonFor: aModel getState: stateSel action: actionSel arguments: args getEnabled: enabledSel label: label help: helpText
"Answer a new button."

	| b |
	b := self on: aModel getState: stateSel action: actionSel.
	b
		arguments: (args ifNil: [{b}]);
		hResizing: #shrinkWrap;
		vResizing: #shrinkWrap;
		label: label ;
		getEnabledSelector: enabledSel;
		setBalloonText: helpText;
		extent: b minExtent;
		hResizing: #rigid;
		vResizing: #rigid.
		^b

Now I do not want to lose the theme so every widget should be able to accept a theme.


### Experiment with the theming architecture

I did the following experiment (object-based inheritance - this is probably not needed).
The code is published on smalltalkhub on StephaneDucasse/PetitsBazars

XPGrowlMorph

	Object < XPGrowlThemeStrategy
		parentTheme := UITheme new

	XPGrowlThemeStrategy < XPGrowlDarkTheme
		parentTheme := PharoDarkTheme new
			growFillColor: Color white

	XPGrowlThemeStrategy < XPGrowlPharo30Theme
		parentTheme := Pharo30Theme new
			growFillColor: Color red
		minTextSize
			^ parentTheme minTextSize

I did the same with XPSimpleButtonMorph


Conclusion:

- Do we gain from object inheritance vs class?
I do not think that we want to change dynamically the theme chain
So I will make the widget strategy class from their theme

	PharoDarkTheme < XPGrowlDarkTheme

	Pharo30Theme < XPGrowlPharo30Theme

- cons we may have duplicated behavior accross theme => traits?

- Do we gain having widgets specific theming strategy
-- cons this generate a lot of classes (so we will see)

- + with inheritance we reuse a lot
- + probably we can reduce the API of UItheme (see below)


- Can we reduce the API of UITheme
	buttonBorderColor
	listBorderColor

	Yes probably creating strategy per morph?

	Pharo3Theme>>buttonBorderColor
		=>
	Pharo30ButtonStrategy>>borderColor

-> but should be experimented


- From a widget point of view, if we want to have possibility to see the same widgets in different theme at the same time
this raises the problem of the injection (or recomputation) of the theme values. Example below:
We want to specify a theme but the new is calling initialize and the values are computed with a default/current theme.


	XPGrowlMorph new
		theme: XPPharo3ThemeStrategy new;
		label: 'The time' contents: TimeStamp now;
		skin;
		openInWorld.

    XPGrowlMorph new
		theme: XPDarkThemeStrategy new;
		label: 'The time' contents: TimeStamp now;
		openInWorld.

		=> Question is it worth because with a default theme we would have it nearly working.


I continued the experiment with PluggableButtonMorph and
- we should remove some duplicated code in UItheme hiearchy :)
- if we do not share behavior in themers (accross widgets) we will have duplicated code and logic.

Side note:

When I see the amount of messages sent to open a morph and the complexity of the theme in addition
to Morphic intrinsic complexity it is obvious that it cannot be fast.
Far too complex. I started to copy simpleButtonMorph and clean it just to understand.
I would like to do the same for the widgets but this is a daunting task.

I really think that Bloc will come to the rescue because simplifying is not an easy task either.
Polymorph is complex and added a lot of hacks on top (because it was designed as a layer).

Not talking about TextMorph and StringMorph different API.
Simple API like label:font: are bogus. Widgets displaying text should have font instance variable

I will try to make the XPPluggableButtonMorph works in Pharo30 and DarkTheme and let you know.

I will continue to split Polymorph and read code around and clean what I can see.


Here is a discussion with 
I said: "Simple API like label:font: are bogus. Widgets displaying text should have font instance variable"

Nicolai
I don't think, this is the real problem or that a font instance variable would solve this. This is how
a simple StringMorph (or TextMorph) and a Label (as part of a widget) differ.

For example: a simple Button. Currently we have that PluggableButtonMorph.
It is a Morph with some shape, colour, border, label and behavior.
The label is actually a TextMorph to heavyvweight for a simple button label. Anyway.

If we move the attributes like background color, border style, label text attributes like
font, color and emphasis to the theme manager, what is left for the ButtonMorph?
Text contents and behaviour - just that.

A UI framework should provide "things" that play
exactly this role. ButtonWidget: Hi, I am a Button, give me some Label and Actions.




### CheckboxButtonMorph and ToggleMenuMorph

We could think that CheckboxButton and ToggleMenuMorph share some logic. But this is not really the case.
Apparently each time a toggle button (bring a menu on the package pane of MCBrowser) is drawn its icon is recalculated.
For example ToggleMenuItemMorph>>offImage is executed.


ToggleMenuItemMorph>>offImage
	"Return the form to be used for indicating an '<off>' marker."
	
	|m form|
	m := CheckboxButtonMorph new
			privateOwner: self owner;
			adoptPaneColor: self paneColor;
			selected: false.
	form := Form extent: m extent depth: 32.
	form fillColor: (Color white alpha: 0.003922).
	form getCanvas fullDrawMorph: m.
	^form

Note that this could be cached.


For ToggleMenuMorph this is different. 

CheckboxButtonMorph>>beCheckbox
	"Change the images and square the border
	to be a checkbox."

	self
		isRadioButton: false;
		onImage: self theme checkboxMarkerForm;
		cornerStyle: (self theme checkboxCornerStyleFor: self);
		borderStyle: self borderStyleToUse
		
UITheme>>checkboxMarkerForm
	"Answer a new radio button marker form. We make it empty because we already have the selected radio button take care of the state."

	^Form extent: 12@12 depth: 32
	
Note that it would be good to have a logic like that favor local value then theme.


I wonder why CheckboxButtonMorph has to be a subclass of ThreePhaseButtonMorph (A button morph with separate images for on, off, and pressed with the mouse.). Currently the use that we have of CheckboxButtonMorph look binary (on/off).

I suggest to define 
	SimpleCheckboxButtonMorph with a simple boolean behavior.