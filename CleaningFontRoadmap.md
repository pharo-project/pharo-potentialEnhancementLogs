This is version v1 21 May 2014 of the roadmap.


### FT problem
One of the core problem is described by the following problem

   * Step 1- FT is set, and we pick a font default font -> arial

   * Step 2- FT sets off, default font is strike font

   * Step 3- FT is set on, default font is still a strike font
	

### Related bug entries

  *  [Done ] https://pharo.fogbugz.com/default.asp?13270

  * When FreeType is on, TextStyle default should not return a StrikeFont
	https://pharo.fogbugz.com/default.asp?13152

  * Comment of FreeTypeProvider refers to FileDirectory and it should not 
	https://pharo.fogbugz.com/default.asp?13153

  * Move embedded fonts from Freetype package into separate package
	https://pharo.fogbugz.com/default.asp?13185


### EmbeddedFreetypeFont

EmbeddedFreetypeFontshould be renamed as EmbeddedFreeTypeFontDescription

###FreeType-Fonts-SourceCode

FreeType-Fonts-SourceCode should be moved out FreeType,	may be we should move it to FontInfrastructure

### FontSet sucks

Apparently this is just a class level helper to load and compile strikefonts.

  * probably remove it. 
  * check if we should revise
  * migrate it as a subclass FontProviderAbstrract
	

### StrikeFontSet 

StrikeFontSet is a subclass of StrikeFont. Do we remove it or not?
		

### [Done] Several tools are packaged in different places 		
This issue has been addressed in 13270. https://pharo.fogbugz.com/default.asp?13270
	
  * Create a new package named FontChooser
	
  * Move AbstractFontSelectorDialogWindow and FreeTypeFontSelectorDialogWindow out of Polymorph-Widgets to a new package FontManager. Then move FontChooser to is.
	

### LogicalFont

It looks like LogicalFont should be more used since this is an abstraction over the fonts

  * [Done - 13270] LogicalFont should be moved out FreeType 			
  * setFontsFromSpec: should be probably moved to TextStyle
  * Some LogicalFonts Athens extensions should be moved to logicalFonts 
    * get* should be moved to logicalFonts
    * asFreeTypeFont to freeType package
  * Same for FreeTypeFont
	
	
### LogicalFontManager

  * may be some information from textStyle should be moved to LogicalFontManager        	
  * [Done - 13270] Extract an abstract package out of FreeType package

### Some Restructurings

  * [Done - 13270] FontFamilyAbstract    -> move out      
    *	FreeTypeFontFamily -> stay in FT
    *	TextStyleAsFontFamily ->
	
  * [Done - 13270] FontFamilyMemberAbstract                

  * [Done - 13270] FontProviderAbstrract -> move out       
     *	FreeTypeFontProvider -> stay in FT
  * Missing StrikeFontProvider (may be we should extract it from FontSet because it is what it does)
			

	
### SourceCodeFonts 

SourceCodeFonts	looks like a hack to get the previously embedded but not registered fonts in the settings?
	
FreeTypeFontProvider class comment sucks and refer to FileDirectory
	
	
### Clean TextSharedInformation

TextSharedInformation was a creation out of the old TextContants dictionary.
The problem is that the people put information that changes inside while normally they should not.
	
TextConstants TextSharedInformation keys 
	#(#CrLfCrLf #DefaultEditMenu #DefaultFixedTextStyle #DefaultTextStyle #pixelsPerInch #CaretForm #CrLf #DefaultMultiStyle #DefaultEditMenuMessages #'Bitmap DejaVu Sans' #CtlW #StyleDecoder)	
	
	
	
### TextStyle	
TestStyle does not hold any FreeType information. TextStyle only refer to StrikeFont
	
We have 106 users of TextStyle. It has a lot of responsibilities:
  * registered fonts
  * screen pixel information
	
	
### StandardFonts

  * this is a facade with 98 references.
  * I wonder if StandardFonts should not be merged with FontManager.
  
### About my changes
I introduced a new little hierarchy StandardFontSelection used by the StandardFonts but I wonder if it makes sense.
  
I wonder if a strategy to collect fonts should not be added to the fontManager because right now all the fonts are queried and this does not make sense.





