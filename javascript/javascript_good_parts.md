# JavaScript the Good Parts


- Curly brace NOT at a newline. Put it in the right.
	- return object literal. 

~~~ javascript
//GOOD, return an object

return {
	ok : false
};

//BAD return nothing. Silent error because of semicolon insertion.
//semicolon is added at the end of return 

return ;
{
	ok : true
};

~~~

- IFFE

~~~ javascript
// better style
(function () {


}());

//than this
function () {


}();

~~~

- Don't use with statement.
	- It's unpredictable, and good stylistic program should always be predictable.

- Always use ===, never ==.
	- except nan...

- Multiple string literals
	- Space after \ character will cause a syntax error.

~~~ javascript 
//correct
var long_line = "abcd \
asdf"

//syntax error
var long_line = "abcd \ 
asdf"
~~~

- Scope
	- JS has a function scope.
	- var statment
		- split into two parts
			- the declaration part gets hoisted to the top of the function. Initializing with undefined.
			- The initalization part turns into an ordinary assignment.
	- Declare all variables at the top of the function.
	- Declare all functions before you call them.
	
~~~ javascript
//This
function foo() {
	...
	var myVar = 0, myOtherVar;
}

//Into This
function foo() {
	var myVar = undefined;
		myOtherVar = undefined;
		...
		myVar = 0;
}

//declare var i at the top
for( var i ...) { ...}
~~~

- Global variables.
	- Global variables are evil, and avoid them if you can.
	- if you have to. use it with uppercase.

- new prefix
	- Forgetting new causes a constructor to clobber global variable without warning
	- Constructor function should be named with InitialCaps.

~~~ javascript
//This means...
var a = b = 0;

//not this
var a = 0, b = 0;

//but this. Creating global variable b
b = 0;
var a = b;
~~~

- Always place curly braces after conditional statement.

- Performance
	- clean code is easier to reason about.
	- premature optimization is the root of all evil.
	- Most of the code has a negligible impact on performance. Only optimize the code that is taking time
	- algorithm replacement is vastly more effective than code fiddling
