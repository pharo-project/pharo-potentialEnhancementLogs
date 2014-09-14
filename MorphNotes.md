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