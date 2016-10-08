## The Awful Parts

**Need to know what all the pitfalls are with these parts.**

###Global variables
These are variables that are visible throughout the code in any scope. They can be **changed at any time** by any part of the program which makes them **unreliable in larger complex programs**. This can also lead to naming conflicts which can cause your code to fail or you to accidentally overwrite a global variable.

Defined in three ways:
* Using a `var` statement **outside** of any function; `var foo = value`;
* By adding a property to the global object (container of all global variables), such as `window` in browsers; `window.foo = value;`
* Using a variable without declaring it with `var`, which makes it an _implied global_; `foo = value`

###Scope
Although JavaScript has block _syntax_ (i.e. is written in blocks) like a lot of other programming languages, it has **functional scope** and _not_ block scope.

Variables should all be declared at the top of the function and not littered throughout the block.

###Semicolon Insertion
Attempts to correct faulty programs by automatically inserting semicolons. **Do not depend on this** as it can hide underlying issues.

Also ensure opening curly braces ({) are on the first line of a statement, otherwise semicolons will be erroneously inserted and cause problems:
```javascript
//Ensure curly braces open on the first line of a statement
return {
	status: true	//for example
};
//instead of
return
{
	status:true
};
```
###Reserved Words
Most JavaScript reserved words are not used in the language but **cannot be used to name variables or parameters**.

If used as the key in object literals, they _must_ be quoted. For example `object - {'case' : value};` or `object['final'] = value;` as _case_ and _final_ are both reserved words.

###Unicode
JavaScript characters are 16 bits which only cover the original Unicode Basic Multilingual Place.

###typeof
Watch out for:
* `typeof null` which returns 'object' instead of 'null'
* incorrect reporting on typeof regular expressions, with some implementations returning 'object' and some returning 'function'
* arrays are objects in JavaScript so `typeof array` will return 'object'

All `object`s are _truthy_ and `null` is _falsy_, so you can use the following to tell them apart:
```javascript
if (my_value && typeof my_value === 'object') {
	//then my value is definitely an object or an array because not only is its 'typeof' an object but it's also truthy (first statement)
}
```

###NaN
* `typeof NaN === 'number'` even though it stands for _not-a-number_
* If you have a chain of formulas that together produce a `NaN` then at least _one_ of them will have generated `NaN`
* Surprisingly `NaN !=== NaN`
* `isNaN(value)` can be used to distinguish numbers from NaN

For numbers, best use your own isNumber formula:
```javascript
var isNumber = function isNumber(value) {
	return typeof value === 'number' && isFinite(value);	//isFinite() rejects NaN and Infinity, but is only good for numbers, not strings
}
```

###Phony Arrays
JavaScript **doesn't have real arrays**, it has _array-like objects_.

* Good: No need to give them dimensions and don't generate out-of-bounds errors
* Bad: Slower than 'real' arrays

To test if value is an array:
```javascript
if (my_value && typeof my_value === 'object' && typeof my_value.length === 'number' &&
	!(my_value.propertyIsEnumerable('length'))) {
		//my_value is definitelyy an array!
}
```
The `arguments` array isn't an array, just an object with a length property.

###Falsy Values
`0`, `NaN`, `''`, `false`, `null` and `undefined` are all _falsy_ values, but **they are not interchangeable**. When testing for a missing member of an object for example, you need to use `undefined` and not `null`.

`undefined` and `NaN` are actually global variables instead of constants but **don't change their values**.

###Object
JavaScript objects inherit members from the prototype chain so they are _never truly empty_.

To test for membership without prototype chain involvement, use the `hasOwnProperty` method or limit your results (for example, to specific types like number so you know you're not dragging in object members from up the prototype for example if that's what's causing the problem).
