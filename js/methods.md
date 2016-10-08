##Methods

###Arrays

####_array_.concat(_item..._)
Produces **new array** copying the original array with the `items` appended to it (does not modify the original array like `array.push(item)` does. If the `item` is an array, its elements are appended.

####_array_.join(_separator_)
Creates a string of all the array's elements, separated by the `separator`. Use an empty string `separator` ('') to join without separation.

####_array_.pop()
Removes _last element_ of array. Returns `undefined` for empty arrays.

####_array_.push(_item..._)
**Modifies the _array_**, appending `items` onto the end. Returns the new `length` of the array.

####_array_.reverse()
_Modifies_ the array by **reversing the order of the elements**.

####_array_.shift()
Removes the _first_ element of the array (does not leave a hole in the array - same effect as using the `.splice(a,b)` method) and returns that first element.

####_array_.slice(_start, end_)
Different to `splice`.

'slice' creates a **new array**, copying from the `start` element and stopping at the element _before_ the `end` value given. If no `end` is given, default is `array.length`.

Negative values for `start` and `end` will have `array.length` added to them and if `start`>`end`, it will return an **empty** array.

####_array_.sort(_comparefn_)
JavaScript has a `sort()` method which was created only to compare strings and therefore sorts numbers incorrectly (it will sort them as 1, 15, 2, 23, 54 for example). Therefore, we have to write a comparison function which returns _0_ if the two elements you are comparing are equal, a _positive number_ if the first element should come first and a _negative number_ if the second element should come first. Then pass this comparison function to `sort()` as a parameter to allow it to sort array elements _intelligently_.

Page 80-82 in the book takes you through various iterations of the comparison functions - for numbers, simple strings, objects and objects with multiple keys (for example if you want to sort objects by first _and_ last names). These should be taken from the book when required.

####_array_.splice(_start, deleteCount, item..._)
Removes elements from the array making sure there are no holes left in the array. It is most popularly used for deleting elements from an array.

It removes the `deleteCount` number of elements from the array starting from the `start` position. If there are `item` parameters passed to it, it will replace the deleted elements in the array with the `items`.

It returns an **array containing the deleted elements**.

####_array_.unshift(_item..._)
Works like `push` but adds items **to the front of the array** instead of the end. Returns the new `length` of the array.

###Function

####_function_.apply(_thisArg, [argArray]_)
The `apply` method invokes a function, passing in the object that will be bound to `this` and _optional_ array of arguments.

###Number

####_number_.toExponentional(_fractionDigits_)
Converts _number_ to a string in **exponential form** (e.g. 3.14e+0). `fractionDigits` (from 0 to 20) gives the number of decimal places.

####_number_.toFixed(_fractionDigits_)
Converts _number_ to a string in **decimal form** (e.g. 3.1415927). `fractionDigits` (from 0 to 20) gives the number of decimal places.

####_number_.toPrecision(_precision_)
Converts _number_ to a string in decimal form (e.g. 3.1415927). The difference from `toFixed` is that `fractionDigits` (from 0 to 20) gives the number of **total digits*.

####_number_.toString(_radix_)
Converts _number_ to a **string**. `radix` is an _optional_ parameter between 2 and 36 and gives the _base_. The default radix is 10.

###Object

####_object_.hasOwnProperty(_name_)
*Does not look at the property chain**. Returns true if the _object_ contains the property `name`.

###RegExp
####_regexp_.exec(_string_)
Most powerful (and _slowest_) regexp method.

Checks the `string` against the _regexp_ (starting at position 0) and returns an **array** containing the matches.
The _regexp_ is set up with various capturing groups and these determine the elements that go in the array:
* the 0 element of the array will contain the part of `string` that matched the _regexp
* element 1 of the array will contain the text captured by the first capturing group in _regexp_
* element 2 of the array will contain the text captured by the second capturing group in _regexp_ and so on
* if the match fails, it returns `null`

If the **_regexp_ contains a `g` flag** (e.g. `var regexp = /[ˆ<>]+|<(\/?)([A-Za-z]+)([ˆ<>]*)>/g;`), there is a lot more to look out for:
* Searching begins at `regexp.lastIndex` (initially zero)
* If a match is found, `lastIndex` becomes the position of the _first character of the match_
* If **no** match is found, `lastIndex` is reset to zero
* If searching for multiple occurrences of a pattern by calling `exec` in a loop, ensure you _reset `lastIndex`_ when exiting the loop and remember `ˆ` only matches _when `lastIndex` is equal to zero_

Example on page 87 of the book is worth reading to improve understanding.

####_regexp_.test(_string_)
Simplest (and _fastest_) regexp method.

If _regexp_ matches the `string` it returns _true_. Otherwise it returns _false_.
**Do not use the `g` flag with this method**.

###String
####_string_.charAt(_pos_)
Returns character at position `po` in the string _starting from 0_. If `pos` is less than zero or bigger than the string itself it return an **empty string**.

####_string_.charCodeAt(_pos_)
Same as `charAt` except it returns the **integer** that represents the _code point value of the character at position `pos`_.
Returns `NaN` is  _string_.length < `pos` < 0.

####_string_.concat(_string..._)
Creates **new string** concatenating various strings. `+` tends to be used instead of this method (e.g. `var cat = 'c'+'a'+'t';`)

####_string_.indexOf(_searchString, position_)
Searches for `searchString` within _string_ starting at position `position` (an optional parameter). If `position` is not provided, search starts at the beginning of the _string_.
Returns the integer _position of the first matched character_ or _-1_ if no match is found.

####_string_.lastIndexOf(_searchString, position_)
Same as `indexOf` but searches **from the end of the string** instead of the beginning.

####_string_.localeCompare(_that_)
Compares _string_ to `that` parameter and returns:
* 0 if _string_ === `that`
* -1 if _string_ < `that`

_NB. 'a' < 'A', comparison is not just in length._

####_string_.match(_regexp_)
Works the same way as `regexp.exec(string)` **if** there is no `g` flag in the `regexp`.

If there is a `g` flag in teh `regexp`, it produces an array of the matches **but excludes the capturing groups**

####_string_.replace(_searchValue, replaceValue_)
Searches for the `searchValue` in _string_ and replaces it with the `replaceValue`.

If `searchValue` is a:
* **string**, only its _first occurrence_ will be replaced with the `replaceValue`
* **regexp with a g flag**, _all occurrences_ will be replaced with the `replaceValue`; otherwise, only the _first occurrence_ will be replaced

If `replaceValue` is a:
* **string**, a `$` value has a special meaning when used in the `replaceValue` that conveys what to replace - see table on page 90 for possible variations on `$`
* **function**, it is called for each match and the _string result of the function_ is used as the replacement text
	* string result of the first call will replace capture group 1 of the _string_ and so on


####_string_.search(_regexp_)
Similar to `.indexOf(string)` but takes a `regexp` instead of a `string`, returning the **position of the first match** (or -1 if there is no match).
The `g` flag is **ignored**.

####_string_.slice(_start, end_)
Creates a **new string** by copying the characters from the `start` position to the character before the `end` position in _string_.

The `end` parameter is _optiona_ and defaults to _string_.length. If either parameter is negative, _string_.length is added to it.

####_string_.split(_separator, limit_)
Creates an **array of strings** by splitting apart _string_ at the points where the `separator` appears (e.g. if the separator is '.', ab.cd' becomes ['ab', 'cd']).
* If separator is an _empty string_, an array of single characters is produced.
* `limit` is _optional_ and determines how many pieces are to be split off from the original _string_.
* The `separator` can be a `regexp` but
	* text from capturing groups within the regexp will be included in the split - e.g. in `var e = text.split(/\s*(,)\s*/);` the commas (,) will each be included as a separate element in the resulting array
	* some systems _ignore empty strings_ when the `separator` is a `regexp`

####_string_.substring(_start, end_)
No reason to use, **use `slice` instead**.

####_string_.toLocaleLowerCase()
Produces a **new string** converted to lower case, _using the rules for the particular locale_ (geography).

####_string_.toLocaleUpperCase()
Produces a **new string** converted to upper case, _using the rules for the particular locale_ (geography).

####_string_.toLowerCase()
Produces a **new string** converted to lower case.

####_string_.toUpperCase()
Produces a **new string** converted to upper case.

####String.fromCharCode(_char..._)
Produces a **new string** from a series of numbers.
`var a = String.fromCharCode(67, 97, 116);    //a === 'Cat'`
_NB. You're calling the prototype here, not replacing 'String' with your own variable._
