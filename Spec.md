Here are some notes for the simplification of Spec
---------------------------------------------------

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
---------------------------------------------------

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
