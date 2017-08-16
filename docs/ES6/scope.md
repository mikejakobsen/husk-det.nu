# Javascript Scope

IIFE (immediately invoked function expression)

In ES5 each of the *A Variable* would return a different value because of the scoping. The first *Console.log* would return the var within the *IIFE function*.

```Javascript
	var a = 2;

	(function IIFE(){
		var a = 3;
		console.log( a ); // 3
	})();
	console.log( a ); // 2
```

However, we can now create declarations which are bound to any block, called (unsurprisingly) block scoping. This means all we need is a pair of { .. } to create a scope. Instead of using var, which always declares variables attached to the enclosing function (or global, if top level) scope, use let:


```Javascript
	var a = 2;
	{
		let a = 3;
		console.log( a ); // 3
	}

	console.log( a ); 2
```

Always use *Let* by default.
