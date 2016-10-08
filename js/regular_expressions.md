##Regular Expressions
> A _regular expression_ is the specification of the syntax of a simple language

Used with `regexp.exec`, `regexp.test`, `string.match`, `string.replace`, `string.search` and `string.split` to interact with string

Quite convoluted and difficult to read as **do not allow comments or whitespace** so a JavaScript regular expression **must be on a single line**

###An Example

`/ˆ(?:([A-Za-z]+):)?(\/{0,3})([0-9.\-A-Za-z]+)(?::(\d+))?(?:\/([ˆ?#]*))?(?:\?([ˆ#]*))?(?:#(.*))?$/`

Breaking it down one portion factor at a time:
* Note that the string starts and ends with a slash `/`
* `ˆ` indicates the beginning of a string
* `(?:([A-Za-z]+):)?`
	* `(?:...)` indicates a _noncapturing group_), where the '...' is replaced by the group that you wish to match, but not save to anywhere
	* Suffix `?` indicates the group is optional, so it could or could not exist in the string - it could even exist more than once
	* `()` around the _([A-Za-z]+)_ indicates a _capturing group_ which is therefore captured and placed in the `result` array
		* They groups are placed in the array in order, so the first will appear in `result[1]`
		* **Noncapturing groups are preferred to capturing groups** because capturing groups have a performance penalty (on account of saving to the result array)
		* You can also have **capturing groups _within_ noncapturing groups** such as [`(?:Bob says: (\w+))`](http://www.rexegg.com/regex-disambiguation.html)
	* `[...]` indicates a character class
	* `A-Za-z` is a character class containing all 26 letters of the alphabet in both upper and lower case
	* Suffix `+` means character class will be matched _one or more times_
	* Suffix `:` is matched literally (so the letters will be followed by a colon in this case)
* `(\/{0,3})`
	* `\/` The backslash `\` _escapes_ the forward slash `/` (which traditionally symbolises the end of the regular expression literal) and together they indicate that the forward slash `/` should be matched
	* Suffix `{0,3}` means the slash `/` will be matched between 0 and 3 times
* `([0-9.\-A-Za-z]+)`
	* String made up of one or more (note the `+` at the end denoting possible multiple ocurrences) digits, letters (upper or lower case), full stops (.) or hyphens (-)
		* Note that the hyphen was escaped with a backslash `\-` as hyphens usually denote a _range_ but in this case is a hyphen within the expression
* `(?::(\d+))?`
	* `\d` represents a _digit character_ so this will be a sequence of _one or more_ digit characters (as per the `+`)
	* The digit characters will be immediately preceded by a colon `:`
	* `(\d+) will be the fourth capturing group in this expression, it is also _optional_ (`?`) and inside a non-capturing group (`(?:...)`
* `(?:\/([ˆ?#]*))?`
	* Another optional grou (`?`), beginning with a literal slash `/` (escaped by the backslash)
	* The `ˆ` at the beginning of character class `[ˆ?#]` means it includes _all_ characters _except_ ? and #
		* This acutally leave the regexp open to attack because too many characters are included in the character class
	* The `*` indicates the character class will appear _zero or more_ times
* `(?:\?([ˆ#]*))?`
	* We've seen everything here before: An optional capturing group starting with a literal `?` (escaped by the backslash) with zero or more characters that are not #
* `(?:#(.*))?`
	* Final optional group beginning with a `#`
	* `.` matches any character _except a line ending character_
* `$` represents the end of a string

* Note: **`ˆ` and `$` are important because they anchor the regexp** and checks whether the string matched against it contains _only_ what is in the regexp
	* If `ˆ` and `$` weren't present, it would check that the string _contained_ the regexp but wouldn't necessarily be only made up of this
	* Using only `ˆ` checks the string _starts_ with the regexp
	* Using only `$` checks the string _ends_ with the regexp

**Another example**
`/ˆ-?\d+(?:\.\d*)?(?:e[+\-]?\d+)?$/i;`

Most of this we have seen before but here are the new bits:
* The `i` at the end means _ignore case_ when matching letters
* `-?` means the minus sign is optional
* `(?:\.\d*)` matches a decimal point followed by _zero or more_ digits (123.6834.4442284 _does not match_)
* Note this expression only uses _noncapturing_ groups

###Construction

3 flags exist in regular expressions: `i` means insensitive - ignore the character case, 'g` means global - to match multiple items and `m` means multiline - where ˆ and $ can match line-ending characters

Two ways to build a regular expression:
1. _Regular Expression literals_ as per the examples above start and end with a slash `/`
	* Here the flags are appended after the final slash, for example `/i`
	* Be careful: `RegExp` objects made by regular expression literals share a single instance
2. Use `RegExp` constructor
	* The first parameter is the string to be made into a `RegExp` object, the second is the flag
	* Useful when all information for creating the regular expression is not available at time of programming
	* Backslashes mean something in the constructor, so these must be doubled and quotes must be escaped

```javascript
//example creating a regular expression object that matches a JavaScript string

var my_regexp = new RegExp("'(?:\\\\.|[ˆ\\\\\\'])*'", 'g');
```

###Elements

####Regexp Choice

`|` provides a match if any of the sequences provided match.

In `"into".match(/in|int/);`, the _in_ will be a match so it doesn't even look at the _int_.

####Regexp Sequence

A _regexp sequence_ is made up of one or more regexp [factors](#Factors). If there are no quantifiers after the factor (like `?`, `*` or `+`), the factor will be **matched one time**.

<a name="Factors"/>
###Regexp Factor

> A _regexp factor_ can be a character, a parenthesized group, a character class, or an escape sequence.

It's essentially a portion of the full `RegExp`, like what we broke down the regexp above into.

* The following special characters must all be _escaped_ with a backslash `\` to be taken literally, or they will take on an alternative meaning: \ / [ ] ( ) { } ? + * | . ˆ$
* The `\` prefix **does not make letters or digits literal**
* When unescaped:
	* `.` matches any character except line-ending
	* `ˆ` matches the beginning of the text when `lastIndex` property is zero, or matches line-ending character when the `m` flag is present
	* Having `ˆ` inside a character class means NOT, so [ˆ0-9] means _does not_ match a digit
	* `$` matches the beginning of the text or a line-ending character when the `m` flag is present

####Regexp Escape

As well as escaping special characters in regexp factors, the backslash has additional uses:
* As in strings, `\f` is the formfeed character, `\n` is new line, `\r` is carriage return, `\t` is tab and `\u` specifies Unicode as a 16-bit hex. But **`\b` is _not_ a backspace character**
* `\d` === [0-9] and `\D` is the opposite, NOT (ˆ) a digit, [ˆ0-9]
* `\s` matches is a partial set of Unicode whitespace characters and `\S` is the opposite
* `\w` === [0-9A-Za-z] and `\W` === [ˆ0-9A-Za-z] but **useless for any real world language** (because of accents on letters, etc)
* `\1` refers to the text captured in group 1 so it is matched again later on in the regexp
	* `\2` refers to group 2, `\3` to group 3 and so on

*`\b` is a _bad part_. It was supposed to be a word-boundary anchor but is useless for multilingual applications

####Regexp Group

Four kinds of groups:
<a name="Capturing"/>* **Capturing:** `(...)` where each group is captured into the `result` array - the first capturing group in the regexp goes into `result[1]`, the second into `result[2]` and so on
<a name="Noncapturing"/>* **Noncapturing** `(?:...)` where the text is matched, but not captured and saved anywhere, making is _slightly faster_ than a capturing group (has no bearing on numbering of capturing groups)
* _Positive lookahead_, a **bad** part: `(?=...)` acts like a noncapturing group except after the match is made, it goes back to where text started
* _Negative lookahead_, a **bad** part: `(?!...)` is like a positive lookahead but only matches if there is no match with what is in it

####Regexp Class

* Conveniently and easily specifies one of a set of characters using square brackets `[]`, for example vowels: `[aeiou]`
* Can shorten specification of all 32 ASCII special characters to **[!-\/:-@\[-'{-˜]** (note that the ' in this piece of code should be a back-tick which I can't use as part of these notes)
* Also allows `ˆ` as the first character after the opening `[` to mean _NOT_ the characters in the character set

####Regexp Class Escape

There are **_specific_ characters that must be escaped in a character class**: -  /  [  \  ]  ˆ

####Regexp Quantifier

A _quantifier_ at the en of a factor indicates how many times the factor should be matched
* A number in curly braces means the factor should match that many times, so `/o{3}` matches _ooo_
* Two comma-seperated numbers in curly braces provide the _range_ of times a factor should match, so `{3,5}` indicates it will match 3, 4 or 5 times
* _Zero or **one**_ times (same thing as saying something is optional) can be `?` or `{0,1}`
* _Zero or **more**_ times can be `*` or `{0,}`
* _One or **more**_ times can be `+` or `{1,}`

**Prefer to use 'zero or more' or 'one or more' matching over the 'zero or one' matching** - i.e. prefer _greedy_ matching over _lazy_ matching
