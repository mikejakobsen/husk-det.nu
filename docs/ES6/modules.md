# Modularity

Higher level of abstraction.

## IIFE

Anonymous function
Invoked when declared
Provides encapsulation
Reduces global scope pollution

No dependency management

```Javascript
	(function() {
		
	}());
```

Snippet: iife

### Revealing Module Pattern - Singleton

Scoped too the 
```Javascript
	var scoreboard = function() {
		var message = "Hi!";
		function printMessage() {
			console.log(message);
		}
	return {
		showMessage: printMessage
		}
	}();
```
