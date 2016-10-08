##Functions

> The best thing about JavaScript is its implementation of functions.

###Function Objects

* Functions are objects linked to _function.prototype_ (which is linked to _Object.prototype_).
* As well as usual object behavior, they can be **invoked**.

###Function Literal

* A function literal has 4 parts:
	* The (reserved) word `function` itself
	* An _optional_ name (unnamed functions are considered _anonymous_ functions)
	* Comma-separated parameters of the function, in parentheses - `(parameters)`
	* Set of statements in curly brakcets to be carried out when the function is invoked - `{statements}`

```javascript
//Format of a function
function name (parameterA, parameterB){
	statements;
}
```

* Functions can be nested within functions and the inner function can access all the parameters of the outer function as well as its own

###Invocation

* Stops the current function from running and tells the function you have invoked both to start and to use the arguments (values in parentheses) you have passed it in the invocation `function (parameters)`
	* If arguments > number of arguments expected, the **extra values will be ignored**
	* If arguments < number of arguments expected, the function will assume **undefined in place of the missing arguments**
	* No error is thrown

* **Note:** The difference between an _argument_ and a _parameter_ is that a parameter is usually what is used in the function literal, when you're setting up the function (almost like the placeholder for the actual values that the function will use when it is active) and an argument is usually the value passed to a function at the time it is invoked
* Parameters `this` and `arguments` are also passed to the function when it is invoked, but their value depends on how the function is invoked

####Method Invocation Pattern

* When a function is **stored as the property of the object** (invoked with a dot . expression) it is called on and is called a _method_
```javascript
myObject.incrementFunction();
```
* The method is **bound to the object** and therefore can use `this` to **retrieve or update values from the object**
* These methods are **highly reusable**
* Because their _object context_ comes from `this` they are considered _public methods_

####Function Invocation Pattern

* When a function is _not_ the property of an object, it is invoked as a _function_
```javascript
var sum = add(3, 4);
```
* These functions are **bound to the global object** (_a "mistake in the design of the language" according to Douglas Crockford)_ and consequently so is `this`[even in inner functions](#nestedFunctions)
* Invoking `this` within an inner function will therefore refer to its _own_ `this` and **not** the one in global scope

**Workaround:** Artificially create a new `this`:
```javascript
myObject.double = function() {
	//in the book, the var here is called `that` but name changed for clarity
	var globalScopeThis = this; //workaround

	var innerFunc = function() {
		globalScopeThis.value = add(globalScopeThis.value, globalScopeThis.value);
	};

	innerFunc(); //invoke innerFunc as function
};

myObject.double();
console.log(myObject.value);
```

####Constructor Invocation Pattern

* When a function is created with `new`, that function contains a link to the function's prototype
* This means that methods that were created for the **prototype function are also available** to the function created using `new`

```javascript
//create a function Quo that takes a string - Quo will be our prototype function as we will see
var Quo = function (string){
	this.status = string;
}

//Now create a get_status method for Quo - this will be a public method
Quo.prototype.get_status = function {
	return this.status;
}

//create a new instance of Quo using the prefix NEW
var myQuo = new Quo("happy");

//because of the use of the new prefix, myQuo is an instance of Quo which means it can access the public method get_status from it's prototype
document.writeIn(myQuo.get_status());     //returns 'happy'
```
* **This style of constructor pattern is not recommended**, there will be better examples in [Chapter 5](#chapter5) - this is noted again in [Appendix B](#new)
* The first letter of a constructor function (in this case Quo) **must _always_ be capitalized**

####Apply Invocation Pattern

* The `apply` method lets you **choose the value to be bound to `this`**
* It also takes the parameters for a function in an array
* Format:   `function.apply(valueForThis, arrayOfParamentersForFunction);`

```javascript
var array = [5, 2]    //will be the parameters for our function
var sum = add.apply(null, array);     //value of 'this' is null and value of sum is 7 as the 'apply' method passes 5 and 2 to the 'add' method
```

###Arguments

* Another default parameter of functions is the `arguments` array which contains all the arguments that were supplied when the function was invoked
* This means you don't have to know the exact number of arguments when you build a function because you can loop through all the arguments provided at invocation with the use of the default `arguments` array
```javascript
//inside the function
for (i = 0; i < arguments.length; i++) {
	dosomething;  //e.g. sum +=arguments[i]
}
```
* `arguments` **lacks all the array methods except .length** because of a bug

###Return

* When a function gets to a `return` statement, it returns immediately **without carrying out the remaining statements in the function**
* A function **always returns a `value`** or if unspecified, it returns `undefined`
* "If the function was invoked with the `new` prefix (used when creating a new object so it **must** return an object) and the `return` value is not an object, then `this` (the new object) is returned instead."

###Exceptions

* A `throw` statement interrupts the execution of the code is used to handle expected exceptions like an incorrect type of argument (e.g. a string where a number is expected)
* Each `throw` statement should have an **exception object** with a `name` holding the type of exception and a `message` with an explanation of it + any other properties you like
```javascript
//Thinking through what exceptions could happen in an add function, the main function contains the throw statement with the exception object
var add = function (a,b) {
	if (typeof a !== 'number' || typeof b !== 'number'){
		throw {
			name: 'TypeError';
			message: 'The add function requires numbers';
		}
	}
	return a + b;
}
```
* When you write a function to use _add()_, you include a `try` block where the exception object from the `throw` statement in _add()_ will pass control to a **single catch clause for all exceptions**

```javascript
//When you use the function later on, add a try block with a catch clause to catch the exception object
var try_it = function () {
	try{
		add("seven");   //will throw an exception as it is not a number
	}
	catch (e) {
		document.writeIn(e.name + ':' + e.message);
	}
}

try_it();    //you could rewrite this function so the argument is passed in here where it is invoked
```

###Augmenting Types

* Adding a method to the prototype of an object `Object.prototype` (or function, array, string, number, regular expression or boolean), you make it available to all the instances of that object so you don't have to use the `prototype` property again
* By augmenting the _basic types_ (essentially the root prototypes), we can improve Javascript overall
* For example, adding a method named _trim_ to remove spaces from the end of strings, available to all String instances in your code:
```javascript
String.method ('trim', function {
	return this.replace(/ˆ\s+|\s+$/g, '');     //uses regular expression
});
```
* To be on the safe side, create a method conditionally, **only when you know the method is missing**

```javascript
//Makes a method available to all functions, ONLY when it definitely does not already exist

Function.prototype.method (methodName, func) {
	if (!this.prototype[methodName]){
		this.prototype[methodName] = func;
		return this;
	}
};
```
* Remember that **_for in_ statements don't work well with prototypes**

###Recursion

* Used when a task can be divided into **simple sub-problems** and a function can _call itself repeatedly_ to solve them
Takes the format:

```javascript
var variable = function functionName (parameters){
	//wrap the statements to be executed and the recursive call in a loop statement so ir doesn't recurse forever
	//statements to be executed in the function;
	functionName(arguments);
};

functionName (initialArguments); //initial call to the funtion
```
* Javascript **does not have _tail recursion optimization_** and therefore does optimize recursive functions - this also means they sometimes fail if they "recurse very deeply"

###Scope

* A _block_ is a set of statements contained in curly brackets {}
* Javacript **does not have block scope** but **does have function scope**
	* All variables declared _anywhere_ within a function are **available everywhere in that function** - i.e. and inner function will have access to the variables of the outer function in which it is defined
	* A variable can be _overwritten_ with a new value in an inner function and that new value's scope will be just the body of the inner function - as soon as you're back out to the outer function, the value of that variable will revert to what it was before the inner function began its execution
	* All variable should be **declared at the top of the function body**

###Closure

* Inner functions have **access to the actual parameters of the outer functions (not copies)**
* If an object is created as a result of a function and assigned to myObject, myObject continues to share access to the variables in the functions that created it (actual variables, not copies)
	* It has access to _the context in which it was created_ - this is _closure_
	* This includes later on, even if _the outer function has completed its execution and returned_, when the inner function is called, it will still have **access to all the variables it had access to at the time it was defined** (i.e. the variables that were _in context_ when the inner function was defined)

###Callbacks

* A _callback function_ is a function passed to another function as a parameter and executed in this other function
* When making a request of a server, use an _asynchronous request_ as asynchronous functions return immediately, therefore freeing up the client
	* In this example, we pass the callback function to the asynchronous request as a parameter so the callback function will only be called when a response is available
```javascript
request = prepare_the_request();
send_request_asynchronously(request, function(response){     //function being passed in as a parameter
	display(response);
});
```

###Module

* A module is a function or object whose contents can be used, but its state and implementation are hidden
* It is essentially using function scope and closures keep the variables and functions contained within as private as well as binding them to a non-global object - **whilst still being accessible**
* Using the _module pattern_ is **widely used and good practice** as it promotes information hiding (avoiding naming conflicts, etc) and encapsulation
	* This is a [good article on how to use the module pattern](http://css-tricks.com/how-do-you-structure-javascript-the-module-pattern-edition/) with examples
* It can also be used to produce **secure objects** (see [durable objects](#DurableObject) below)
	* Methods contained in the object do not make use of `this` or `that` so it becomes impossible to change them from outside of the object except in ways explicitly permitted by the methods (like passing them a parameter)
	* The methods can be _replaced_ but the secrets of how these methods function (like how they generate a number for example) can't be revealed because they are not tied to a global object
```javascript
var Serial_maker = function() {

	//all variables defined in this object are now fixed and hidden from anything outside this function
	//see page 42 of book for full example
};
//calls to methods passing them parameters are made here
```
* **Note:** While Javascript variables are usually lowercase, there is some convention around capitalizing the first letter of a Module

###Cascade

* Some methods return nothing, albeit `undefined`
* If we alter these methods to **return `this` instead of `undefined`**, they return the object which can then be passed to the next method, e.g `getElement(myBox).move(350,150)` gets the element and then passes is to the _move_ function for the next action
	* This enables _cascades_, where you **call many methods on the same object in sequence because the object is passed from one method to the next** (usually separated by `.` as above)
* Cascades also stop you from trying to do too much in one method and makes your code more descriptive

###Curry

* A `curry` method allows you to _partially evaluate_ an existing function
	* An example is below where the function _expects **two** arguments_, but it is first invoked with only **one**  (in this case using `curry` as in `add.curry(10);`) and then later passed the second argument
* It can also be explained as transforming a function that takes multiple arguments (`add(a,b)`) into a chain of functions that take a single argument each (`addA = add(A); addA(B);` where the two functions are now `add()` & `addA()`)

```javascript
//set up a simple function that we will customise with curry
var add = function (a,b){
	return a + b;
}

var addTen = add.curry(10);      //passes 10 as the first argument to the add() function
addTen(20);                     //The use of the curry method in addTen means addTen === add(10, 20);
```
* Javascript **does not have a `curry` method** natively but this can be added to the `Function.protoype`:
```javascript
Function.method('curry', function() {
	var slice = Array.prototype.slice,
		args = slice.apply(arguments),
		that = this;
	return function () {
		return that.apply(null, args.concat(slice.apply(arguments)));
	}
});
```

###Memoization

* Storing the results of previous operations in objects (such as arrays) allows them to be reused **without having to keep recalculating the value** - this optimization is called _memoization_
	* Adding an object to store the results _memoizes the function_
* Particularly useful when a function is **recursive** and uses the results of its previous iteration in the current iteration
* A _memoizer_ function can be created to help memoize future functions:
```javascript
var meoizer = function (memo, fundamental) {
	var shell = function (n) {
		var result = memo[n];
		if (typeof result !== 'number') {
			result = fundamental(shell, n);
			memo[n] = result;
		}
		return result;
	}
	return shell;
}
```
