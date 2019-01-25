Shifted-Text
=======================
A proposal for simplifying text formats with hierarchical content

Anybody with some experience in coding has stumbled over the topic `tab` characters (`\t`) versus `space` characters (` `). Relentlessly people have fought in both trenches and no conclusion has ever been reached, since both have their advantages and disadvantages. What I am going to propose has many advantages of both sides.

Indentation has long been used to allow the human eye to directly recognize the hierarchical structure of markup languages (`XML`, `HTML`) or source code files (`.c`, `.js`). The designers of some programming (and markup) languages (e.g. Python, Haskell, yml) have even made indentation part of tha language's syntax. It is a useful tool. However it requires a lot of extra bytes and thus is not very bandwidth friendly. But what if there was a way to have both: visual hierarchies and low bandwidth?

ASCII to the rescue: In the ASCII control character set there are some characters that mostly retain a historical meaning and are not used widely anymore. Two specific characters stuck out to me: `shift-in` (ASCII character number 15) and `shift-out` (ASCII character number 14). It was as if they called out to me, as if they were meant to fulfill this purpose.

The idea now is really simple: 

A single `shift-in` character means that a new line begins with an indentation one level deeper than the line before. _Multiple_ `shift-in` characters characters mean that a _single_ new line begins with an indentation as many levels deeper than the line before as there are `shift-in` characters.

A single `shift-out` character analogously means that a new line begins with an indentation one level lower than the line before. _Multiple_ `shift-out` characters characters mean that a _single_ new line begins with an indentation as many levels lower than the line before as there are `shift-out` characters.

An example should make this clear:

```
a line of text followed by a `shift-in` character
	this line on the other hand is followed by a `shift-out` character
this line is followed by 2 `shift-in` characters
		this line is followed by a `linefeed` character
		this line is followed 2 `shift-out` characters
the end.
````

Any source file that conforms to this format (`Shifted-Text`) begins with an optional Byte Order Mark (BOM) from one of the unicode encodings. If it does not, UTF-8 is assumed, but other encodings are allowed. 

After the BOM follows a `shift-out` (`\14`) character. 

After the `shift-out` character follows a single optional _hexadecimal digit_ (from `1` to `F`) donating the _breadth of the indentation_ if it was expressed in space characters. If there is no number the breadth of the indentation defaults to 4 characters. 

After this (or instead of it) follows an optional _two digit hexadecimal number_ denoting the _width of lines_ (allowed values range from 16 (0x10) to 255 (0xFF), at which a soft-wrap linebreak _must_ occur and the line after the soft-wrap must occure and any line in the soft-wrapped block must be indented one level deeper than the original line. There is no default line-width, e.g. if nothing is specified lines may or may not be soft-wrapped by the editor according to user preference.

Then follows a `shift-in` (`\15`) character. After this begins the content. 

In the content every `shift-in` character increases indentation and every `shift-out` (`\14`) character decreases indentation while _multiple_ consecutive `shift-in` characters correspond to a _single_ new line (with an increased indentation by as many levels as `shift-in` characters). Analogously _multiple_ consecutive `shift-out` characters correspond to a _single_ new line (with decreased indentation by as many levels as `shift-out` characters).

New lines without any change in indentation are expressed using `linefeed` characters – across all systems.

Tab characters (`\t`) work as you would expect them with a minimum length of 1 and a maximum length equal to the breadth of the indentation as defined at the beginning of the `Shifted-Text` file. Thus consistency is fully achieved.

That's it folks. For this to take a foothold it must be adopted by the development teams of your favorite source editors and compilers. So please do propose it to them. I would be happy even more if the language designers would make it the default for their compilers. I have already been programming using this file format and it makes quite a few tasks a lot easier.
