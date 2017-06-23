### renames
 * withFirstCharacterDownshifted -> uncapitalized
 * findBetweenSubStrs: delimiters -> findBetweenSubStrings:
 * skipAnySubStr: delimiters startingAt: start -> skipAnySubStrings: delimiters startingAt: start 
 * padLeftTo: length with: char -> paddedLeftTo: length with: char 
 * pad* -> padded* (with deprecation)
 * occursInWithEmpty: prefix caseSensitive: aBoolean  -> kill kill
 

### recategorize

* lastIndexofPKSignature should be package in compression.
* beginsWithEmpty: prefix caseSensitive: aBoolean -> beginsWith: prefix caseSensitive: aBoolean
* instead of asPluralBasedOn: should be removed and a small class copying the ruby one should be created.
* wordBefore: should be package with TextEditor

### To further examinate

- withSqueakLineEndings (CR = mac convention)
- withInternetLineEndings (CRLF = windows convention… but why would that be internet convention?)
- replace withCR with an expandSpecialCharacters following the C \n \r
- It would be nice to have comparators that can be passed around. Right now compare:with:collated: is expected an array. To think about it.




### look at copy usage

the way string are copied is a bit inconsistent some uses copy other new:

	asLowercase
		"Answer a String made up from the receiver whose characters are all 
		lowercase."
	
		^ self copy asString translateToLowercase

vs

	asOctetString
		"Convert the receiver into an octet string, if possible"
		"(IE, I am a WideString containing only character with codePoints < 255, so all of them fit in a latin1-string)."
		| string |
		string := String new: self size.
		1 to: self size do: [:i | string at: i put: (self at: i)].
		^string


asLowercase is really fast because it uses the table of string for the first 256 characters



## done

### candidates for removal.

* [X] asLegalSelector
* [X] asIdentifier:
* [X] aPathName
* [X] [submitted in in 14094](https://pharo.fogbugz.com/f/cases/14094)
    * endOfParagraphBefore:
    * tabDelimitedFieldsDo: no sender
    * splitInteger (deprecated)
    * padded: leftOrRight to: length with: char (depreacted
    * do: aBlock toFieldNumber: aNumber

### renames

* [X] asSmalltalkComment -> asComment
* [X] asUncommentedSmalltalkCode -> asUncommentedCode
* [X] Move convertToSystemString to ZipArchive for the moment (moved to compression package)
* [X] endsWith: -> isEndingWith: We will not do it!!! It was a nice idea but...  lots of senders, coherent with startsWith: and SequenceableCollection
