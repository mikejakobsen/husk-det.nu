##The Bad Parts

**Avoid these altogether**

* `==` **and** `!=`: Don't function properly when result is false, use `===` or `!==` instead
* `with` **statement**: Intended to provide a shortcut to properties of an object but results vary every time it is run
* `eval`: Adds unnecessary complication and compromises the security of the application
	* Giving string arguments to `setTimeout` and `setInterval` should also be avoided as this makes them act like `eval`
* `continue` **statement**: Forces a loop into its next iteration but the code is usually much improved when re-written _without_ `continue`
* `switch` **fall through**: In a `switch` statement, each `case` falls through to the next `case` unless you explicityly disrup the flow, but using these _intentional_ fall throughs makes the _unintentional_ ones that are causing errors basically impossible to find
	* This is one of those parts of JavaScript that appears useful but you're better off avoiding because it's occasionally very dangerous
* **Block-less statements**: _Always_ use curly braces `{}` to block in statements so as to avoid misinterpretation and aid error finding
* **Bitwise operators**: Shouldn't really be doing this kind of manipulations because they are quite slow in JavaScript, therefore there shouldn't be a need to use `&`, `|`, `ˆ`, `˜`, `>>`, `>>>` or `<<`
	* This doesn't mean you can't use `&&` for example
* `++` **and** `--`: This one seems debatable to me; Douglas Crockford finds it makes his coding style much more cryptic and difficult to read (the book uses `+=1` and `-=1` instead)

**The function statement vs the function expression:**
To use JavaScript well, important to understand that **functions are values**.
* A function _statement_ is shorthand for a var statement with a function value, so `function foo() {}` (a function statement) means pretty much the same as `var foo = function foo(){};` (a function expression)
* Logically, to write the language well you should define a function before using it, but in JavaScript, function statements (using just `function foo(){}`) are _hoisted_ to the top of the scope in which they are defined - this encourages sloppy programming and should be avoided
* function statements also don't function consistently in `if` statements
* if you need to start a function expression with the word _function_, **wrap it in parentheses** (), or JavaScript assumes it's a function _statement_

**Typed wrappers:**
**Don't use `new Boolean` or `new String` or `new Number`**, it's completely unnecessary. Also avoid `new Object` and `new Array` and use `{}` and `[]` instead.

**`new` operator:**
Functions that are intended to be used with `new` (conventionally starting with a capital letter) should be avoided (don't define them) as they can cause all kinds of issues and complex bugs which are difficult to catch.

**void**:
In JavaScript, this actually _takes_ a value and _returns_ `undefined`, which is hugely confusing and not helpful. **Don't use it**.
