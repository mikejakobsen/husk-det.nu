##Grammar

Always use // for comments, even multi-line ones to avoid having to escape `/*` characters.

###Numbers

* There is a single, 64-bit floating point number type.
* `NaN` (Not-a-Number) is not equal to any value (including itself) and is essentially an **illegal number value**, but _typeOf(NaN)===number is true_
* Use `isNaN(number)` to check for NaNs


###Strings

* 16-bit character set and don't have character tpes.
* Backslashes (\\\) are used for **escaping characters** that could cause problems in strings.
* Strings are **immutable**.


###Statements

* _Inside_ a function, the var statement creates variables local to that function
* _switch, while, for_ and _do_ statements can have an optional **label**  which can be used with `break` and `continue` to provide more precise [control over exactly which statement to break or continue](http://www.tutorialspoint.com/cgi-bin/practice.cgi?file=javascript_19). Format: `labelname: statement` and then `continue labelname;`
* A _block_ (statements with {}) **do not create new scope** - the variables are local to the function, not the block. So **always define variables at the top of the function** and not inside the blocks
* _falsy_ values:
	* false
	* null
	* undefined
	* Empty string ' '
	* The number 0
	* The number NaN
* All other values are _truthy_ including all objects & the string 'false'
* If no matches are found in `case` statements, the optional default statement is executed, otherwise the matching case statement is carried out
* When using a _for in_ loop, usually a good idea to use `hasOwnProperty(variable)` to **make sure the property belongs to the object you want** and is not instead an inherited property from the prototype chain:
```javascript
for (myvariable in object) {
		if (object.hasOwnProperty(myvariable)) {
		... //statements to be executed
		}
}
```
* A _do while_ statement is always executed at least once as the while condition is only checked after the first iteration of the loop
* `catch` clause in a _try_ statement **must create a new variable** that will catch the exception object
* Scope of `thro` statement is the `try` block it's in, or the `try` of the function it's in
* If there is no `return` statement, `return===undefined`
* `break` exits the statement and `continue` forces a new iteration of the loop, both with the optional _label_ mentioned above

###Expressions

* For `expression ? expression2 : expression3`, if expression is _truthy_, execute expresion2; it it's _falsy_, execute expression3
*  _Invocation_ is `(expression1, expression2, etc)`
*  _refinement_ is either `.name` or `[expression]` as used in an array

###Literals

* _Names_ or _strings_ used for specifying new objects **object literals** or arrays **array literals**
* Properties of the object are expressions and must be known at compile time

###Functions
* A function literal defines a function value
