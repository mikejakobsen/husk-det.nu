##Inheritance
>Javascript is a prototypal language, which means that objects inherit directly from other objects

Main benefit of inheritance is **code reuse** - you only have to specify differences.

Javascript can _mimic_ classical inheritance but has a much **richer set of code reuse patterns**
* This chapter looks at the more straightforward patterns but it is always best to **keep it simple**

###Pseudoclassical

* The pseudoclassical code reuse pattern essentially has constructor functions (functions invoked using the `new` prefix) work like classes to mimic the classical structure
	* All properties are public
	* If you forget to use the `new` prefix, `this` is not bound to the new object - it is instead bound to the global object and you'll be unwittingly altering these instead!

* There is no need to use it, **there are better code reuse patterns in JavaScript**

###Object Specifiers

Rather than: `var myObject = maker (f, l, m, c, s)` which has too many parameters to remember in the right order, use an _object specifier_:
```javascript
var myObject = maker ({      //note curly braces
	first: f,
	last: l,
	state: s,
	city: c
	}
;)
```
to contain them. They can now be **listed in any order**

Also useful to pass object specifiers to JSON ([see Appendix E notes](#AppendixE))

###Prototypal

* Zero classes, **one object inherits from another**
* Create an object literal of a useful object and then make an instance of it using the format `var myObject = Object.create(originalObjectName)`
* When you then customise the new object (adding properties or methods through the dot notation for example), this is _differential inheritance_, where you specify the **differences from the original object**

###Functional

* **All properties of an object are visible** (Javascript has no classes so there is no such thing as a 'private variable' which can only be seen within a class as per other languages)
* When you use a _function_ to create your original object and the same with the object instances, you're essentially utilising Javascript functional scope to create private properties and methods
The below is an example of how you would create an original object, the `name` and `saying` properties are now completely private and only accessible to the `get_name` and `says` method

```javascript
var mammal = function (spec) {
	var that = {};    //that is a new object which is basically a container of 'secrets' shared to the rest of the inheritance chain

	that.get_name = function () {
		return spec.name;
	};

	that.says = function () {
		return spec.saying || '';  //returns an empty string if no 'saying' argument is passed through the spec object when calling mammal
	};
	return that;     //returns the object that contains the now private properties and methods (under functional scope)
}

var myMammal = mammal({name: 'Herb'});
```
Creating an object 'cat' can now inherit from the `mammal` constructor and only pay attention to the differences between it and `mammal`:
```javascript
var cat = function (spec) {
	spec.saying = spec.saying || 'meow';   //if spec.saying doesn't already exists, make it 'meow'
	var that = mammal(spec);      //here the object 'container of secrets' is set up inheriting from mammal already

	//functions and property augmentations happen here

	return that;      //as above
}
```
* Requires less effort and gives **better encapsulation** and **information hiding** than the pseudoclassical pattern, as well as **access to super methods** (see page 54 of book for super method example)
<a name="DurableObject">
* An **object** created using the functional pattern _and_ making **no use of `this` or `that`** is a _durable object_ and cannot be compromised by attackers
* If you do want something to have access to the object's private properties and methods, you pass it the `that` bundle (i.e. your 'container of secrets')

###Parts
* An object can be composed out of a set of parts
	* For example, you can create a function that provides the object it is passed with a number of methods (which are defined in this function), where each method is a part that is added to the object
