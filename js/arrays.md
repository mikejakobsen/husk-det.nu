##Arrays

Javascript only has **array-like objects** which are slower than 'real' arrays.

**Retrieval and updating** of properties works the **same as with an object _except with integer property names_**.

Arrays have their **own literal format** and their own set of methods

###Array Literals

* An array literal is a **pair of square brackets surrounding zero or more comma-seperated values** `[a, b, c, etc]`
	* The first value will get the property name '0', the second will be '1' and so on
* Javascript allows an array to contain **any mixture of values**

###Length

* If you add to the array, the `length` property will increase to contain the new element - it will not give an error
* If you set the `.length` to a smaller number than the current length of the array, it will **delete any properties with a subscipt >= the new `length`**
* The `push()` method is sometimes useful to add an element to the end of an array
`numbers.push('go')    //adds the element 'go' to the end of the numbers array`


###Delete

* Elements can be deleted from the array object using `delete` but this **leaves a hole in the array**
* Use `array.splice(keyInArray, howManyElementsToDelete)` which changes the keys for the remaining values in the array so there is no hole left
	* May be _slow_

###Enumeration

* A `for` statement can be used to iterate over all the properties of an array (as it is an object)
* **Do not us `for in`** as it does not iterate through the properties in order and sometimes pulls in from furhter up the prototype chain

###Confusion

> The rule is simple: when the property names [keys] are small sequential integers, you should use an array. Otherwise, use an object.

* Arrays are most useful when property names are integers _but_ they can also accept strings as property names
* Javascript doesn't have a good way of telling an object from an array as `typeof array === object`
* To accurately detect arrays, have to define our own function:
```javascript
var is_array = function (value) {
	return Object.prototype.toString.apply(value) === '[object Array]';
	//apply(value) binds `value` to `this` & returns true if `this` is an array
}
```

###Methods

* Array methods are stored in `Array.prototype` which can be augmented using the format:
```javascript
//capital A in Array means this refers to the prototype
Array.method('reduce', function (parameters){
	//define variables and function
	//return a value
});
```
* Remember, **every array inherits and can use the methods you add to `Array.prototype`**
* You can also add methods _directly to an array_ because they are objects
	* `myArray.total = function () { //statements to execute; }` adds a 'total' function to the array `myArray`
* **DO NOT USE:** `Object.create()` will create an object - lacking the `length` property - not an array.

###Dimensions

* Using `[]` will create an empty array as they are not initialized in JavaScript
* Accessing a missing element will give you `undefined`
* If you have an algorithm that relies on the array not being empty and not having `undefined` values, you can write a function that will prep your array to have a certain number of defined values, essentially initializing it with certain values in place
	* An `Array.dim` function is oulined on page 63 which will allow `var myArray = Array.dim(10,0)` to make an array with 10 zeroes starting from the first position in the array(0)
* Javascript only has **one dimensional arrays** but **_can_ have arrays of arrays**
* Two dimensional arrays (matricess) will have to be set up by the programmer
	* page 63 gives a method for this and for explicitly setting cell values so as not to have an empty matrix
