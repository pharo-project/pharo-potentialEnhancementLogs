Here are some notes for the simplification of Spec
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