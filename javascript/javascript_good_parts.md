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

- Use strict in a long run

- Bad parts should be avoided.
	- it is there because of...
		- good intentions
		- haste
		- legacy
	- But the problem is that they are not useless...

- An object is a dynamic collection of properties
	- get
		- object.name
		- object[expression]
	- set
		- object.name = value;
		- object[expression] = value;
	- delete
		- delete object.name
		- delete object[expression]
 
- object literals

~~~ javascript
var my_object = {foo: bar};

//more control now

var my_object = Object.defineProperties(
		Object.create(Object.prototype), {
	foo : {
		value: bar,
		writeable: true,
		enumerable: true,
		configurable: ture
	}
});
~~~

- Classes v Prototype
	- js use prototype
		- make an object that you like
		- create new instance that inherit from that object
		- customize the new objects.
		- taxonomy and classification are not neccesary...

- Delegation
	- Differential Inheritance
	- "anything that I dont know how to do, ask that other object"

- keys must be strings

- Objects are...
	- function
	- Number
	- Boolean
	- RegExp
	- ... etc

- Numbers
	- 64-bit floating point (double)
	- number literals
	- associative law does not hold for number over 9 quadrillion.
		- (a + b) + c === a + (b + c)
	- decimal fractions are approximate
		- a = 0.1, b = 0.2, c = 0.3
		- (a + b) + c === a + (b + c)
	- number methods... (look into these?)
		- toExponential
	- First class objects
		- can be stored as variable
		- can be passed as a paramenter
		- can be returned from a function
		- can be stored in an object
		- can have methods
	- NaN
		- not a number
			- but is a number
		- result of undefined or erroneous operations
		- toxic : any arithmetic operation with NaN as an input will have NaN as result
		- NaN === NaN is false
		- NaN !== NaN is true
	- x == x + 1
		- infinity
		- Number.MAX_NUMBER
		- 9 quadrilion
		- BUT NOT NaN

- Strings
	- a sequence of 0 or more 16-bit unicode characters
	- no separate character type
	- immutable
	- \ escapement
	- use " for external strings.
	- use ' for internal strings and character
	- '+' can concatenate or add.
	- convert number to string
		- Number(str)
		- +str
		- parseInt(str, 10)
			- stops at the first non-digit character
			- leading zero makes js think it's octal. so enforce base 10 always by providing the second parameter

- Array
	- inherits from object
	- indexes are converted to strings and used as names for retrieving values.
	- efficient in sparse array, but not usually...
	- dont have to specify type
	- length property is 1 larger than the highest integer subscript
	- don't use for in with array
	- don't use dot notation 
	- sort function will change element to string and then sort
	- delete arr[number] will leave holes in the array
	- use arr.splice(number, 1)
		- start @ number and delete 1 element.
		- removes the element and renumbers all the following elements
		- slow
	
~~~javascript
myList = ['oats', 'peas', 'beans'];

myList[myList.length] = 'barley';
~~~


- Arrays vs Objects
	- use objects when the names are arbitrary strings
	- use arrays when the names are sequential integers 

- All values are objects
	- except null and undefined
		- null
			- the value that isn't anything 
		- undefined
			- the value that isn't even that
			- missing members in objects
			- default value for variables and parameters.

- typeof
	- typeof(array) === object
	- typeof(null) === object

- Falsy values
	- false
	- null
	- undefined
	- ""
	- 0
	- NaN
	- Truthy values are anything other than that

- JS is loosely typed, not untyped.

- References
	- object are not passed by value. Passed by reference.
	- === operator compares object references.
		- true only if they are the same object

- Identifiers
	- starts with a letter or _ or $
	- all variables, parameters, members, and function names start with lower case.
	- except constructor functions which start with upper case
	- $ should be reserved for machines

- %
	- the remainder operator, not the modulo operator
		- remainder operator takes sign from the first operattor

- &&
	- if first operand is truthy
		- then result is second operand
		- else result is first operand

- ||
	- if first operand is truthy
		- then result is first operand
		- else result is second operand

- bitwise
	- convert the operand to a 32-bit signed integer, and turn the result back into 64-bit floating integer.

- break
	- staement can have labels.
	- break statements can refer to those label

~~~ javascript
loop: for (;;) {
	...
	if(...) {
		break loop;
	}
	...
}
~~~

- For in statement

~~~ javascript
for(name in object) {
	if (object.hasOwnProperty(name)) {
		//name is the key of current member
		//object[name] is the current value
	}
}
~~~

- Throw
	- throw anything

~~~ javasript 
throw new Error(reason);

throw {
	name : exceptionName,
	message: reason
};

//~~~~~

try {
	...
} catch(e) {
	switch (e.name) {
		case 'Error': 
			...
			break;
		default:
			throw e;
	
	}
}
~~~
