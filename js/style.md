##Style
> JavaScripts's loose typing and excessive error tolerance provide little compile-time assurance of our programs' quality, so to compensate, we should code with strict discipline.

* We should **avoid** the _bad parts_ of JavaScript, but also the **useful parts that can be occasionally dangerous**
>the likelihood a program will work [as intended] is significantly enhanced by our ability to read it

* Must be written in a clear, consistent style, including:
	* Good use of whitespace
	* Put at most one statement on a line
	* If you have to break a statement into 2 or more lines, indent the 2nd line onwards (an extra four spaces)
	* _Always_ use blocks (curly braces {}) with structured statements like `if` and `while` to avoid confusion on what the statement is actually doing
		* Put the opening brace `{` on the same (first) line as the statement to avoid JavaScript's [semicolon insertion](#SemicolonInsertion) issues - i.e `if (a) { ...`
	* Use line comments `//comment` and not block commenting (unless you're _commenting out_ code)
	* Declare all your variables at the *beginning of the function*, due to JavaScript's functional scope
> I use a single global variable to contain an application or library. Every object has its own namespace, so it is easy to use objects to organize my code. Use of closure provides further information hiding, increasing the strength of my modules.
