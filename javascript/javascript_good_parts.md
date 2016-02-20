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
------

Functions expression

- optional name
	- recursively or debugger uses it

- funciton objcts are first class
	- maybe passed as an argument to a function
	- maybe returned from a function
	- assigned to a variable
	- stored in an object or array
- function objects inherit from 

#####a var declared anywhere within a function is visible everywhere within the function

function statement

- mendatory name
- function statement is a short-hand for a var statement with a function value.

~~~ javascript
// this is
function foo() {}

//basically this...
var foo = function foo() {};

//which in turn expands to this.
var foo = undefined;
foo = function foo() {};
~~~

function expression vs function statment

- if the first token in a statement is function, then it is a function statement.

Declare all variables at the top of the function.

Declare all functions before you call them.

two psudo parameters

- arguments & this
	- function is invoked to its parameters, it also gets special parameter called arguments.
	- it contains all of the arguments from the invocation
	- array like object
	- argument.length is the number of arguments passed
	- weird interaction with parameter...

example

~~~ javascript
function sum() {
	var i,
		n = arugments.length,
		total = 0;
	for (i = 0; i < n; i += 1) {
		total += arguments[i];
	}
	return total;
}

var ten = sum(1, 2, 3, 4);
~~~

this

- this param contains a reference to the object of invocation
- allows a method to know what object it is concerned with.
- allows a single function object to service many objects.
- key to peototypal inheritance.

Invocation

- a function called with too many arguments, the extra arguments are ignored
- too few of arguments, rest will be undefined.
- no implicit type checking on the arguments

####method form

~~~ javascript
// method form
thisObject.methodName(arguments)
thisObject[methodName](arguments)
~~~

- when a function is called in the method form, this is set to thisObject, the object containing the function
- allows methods to have areference to the object of interest.

####function form

~~~ javascript
// function form
functionObject(arguments)
~~~

- when a function is called in the function form. this is set to the global object.
- inner function does not get access to the outer this
	- helper function does not have the reference.
	- work around : create that var at the ouside function and pass it in.

####constructor form

~~~ javascript
new FunctionValue(arguments)
~~~

- when a function is called with the new operator, a new object is created and assigened to this.
- if no explicit return value, this will be returned

#### apply form

~~~ javascript
funcitonObject.apply(thisObject, arguments)
funcitonObject.call(thisObject, arguments....)
~~~

- explicitely specifying thisObject.
- can also take an array of params or a sequence of param.

-----

subroutine

- function, lambda, func... etc
- why?
	- code reuse
	- decomposition
	- modularity
	- expressiveness
	- higher order

- closure
	- context of an inner function includes the scope of the outer function.
	- an inner function enjoys that context even after the parent function have returned.
	- function scope acts like block scope

~~~ javascript
// bad because of global

var name = ['zero', 'one', 'two',
			   'three', ...]

var digit_name = function(n) {
	return names[n]
};

alert(digit_name(3));

//----

//bad, because have to create 10 elements every invocation

var digit_name function(n) {
	
	var name = ['zero', 'one', 'two',
				   'three', ...]

	return names[n]
}

//-----

//closure
  
var digit_name = (function(n) {
	var name = ['zero', 'one', 'two',
				   'three', ...]

	return function (n) {
		return names[n];
	};
}());

alert(digit_name(3));

//------

//lazy (don't do this), break first class-ness

var digit_name = function (n) {
	var name = ['zero', 'one', 'two',
				   'three', ...]

	digit_name = function(n) {
		return names[n];
	};
	return digit_name(n);
}
~~~

-----

Psudoclassical

- if we replace the original prototype object, then we can inherit another object's stuff.


~~~ javascript
function Gizmo(id) {
	this.id = id;
} 

Gizmo.prototype.toString = function() {
	return "gizmo" + this.id;
}

//---

function Hoozit(id) {
	this.id = id;
	
}
Hoozit.prototype = new Gizmo();
Hoozit.prototype.test = function (id) {
	return this.id === id;
}

//----
Nicer

var gizmo = new_constructor(Object, function (id) {
	this.id = id;
}, {
	 toString: function() {
	 	return "gizmo " + this.id;
	 }

});

var hoozit = new_constructor(gizmo, function (id) {
	this.id = id;
}, {
		test: function (id) {
			return this.id === id;
		}
	}
});
~~~  

function as module

~~~ javascript
(function() {
	var ...
	function ...
	function ...
}());

//-----

var singleton = (function () {
	var privateVar;
	function privateFunc(x) {
		... privateVar...
	}
	return {
		firstMethod: function(a, b) {
			...privatevar
		},
		secondMethod: function (c) {
			...privateFunction()...
		}
	};
}());
~~~

Power constructors


1. make an object

- object literal, new, object.create, call another power constructor

2. define some variables and function

- these become private members


3. augment the object with privileged methods.
4. return the object

~~~ javascript

// will not use new prefix
function myPowerConstuctor(x) {
	var that = otherMaker(x);
	var secret = f(x);
	that.priv = function() {
		... secret x that ...
	};
	return that;
}
~~~

~~~ javascript
function gizmo(id) {
	return {
		id: id,
		toString: function() {
			return "gizmo" + this.id;
		}
	};
}

function hoozit(id) {
	var that = gizmo(id);
	that.test = function(testid) {
		return testid === this.id
	};
	return that;
}

//privacy

function gizmo(id) {
	return {
		toString: function() {
			return "gizmo" + id;
		}
	};
}

function hoozit(id) {
	var that = gizmo(id);
	that.test = function(testid) {
		return testid === this.id
	};
	return that;
}

//super method

function hoozit(id) {
	var secret = {};
	var that = gizmo(id, secret);
	var super_toString = that.toString;
	that.test = function (testid) {
		
	};

}
~~~

~~~ javascript
function memoizer(memo, formula) {
	var recur = function(n) {
		var result = memo[n];
		if (typeof result !== 'number') {
			result = formula(recur, n);
			memo[n] = result
		}
		return result;
	);
	return recur;
};

var factorial = memoizer([1,1], function(recur, n) {
	return n * recur(n - 1);
});

var fibonacci = memoizer([0,1], function(recur, n) {
	return recur(n - 1) * recur(n - 2);
});

~~~

Don't make function in a loop

- it can be wasteful b/c a new function object is created on every iteration
- it can be confusing b/c the new function closes over the loop's variables not over their current value
 
~~~ javascript
//creating event handler in a loop

for(var i ...) {
	div_id = divs[i].id
	divs[i].onclick = function () {
		alert(div_id);
	};
}

// will not work because the loop's variable not over their current value

var i;
function make_handler(div_id) {
	return function() {
		alert(div_id);
	}
}

for (i ...) {
	div_id = divs[i].id;
	divs[i].onclick = make_handler(div_id);
}
~~~

------

Problems 

- Write a function that takes an argument returns that argument

~~~ javascript

function identify(n) {
	return n;
}
~~~

- write two binary functions, add adnd mul, that take two numbers and return their sume and product.

~~~ javascript
function add(i, j) {
	return i + j;
}

function mul(i, j) {
	return i * j;
}
~~~

- write a function that takes an argument and returns a function that returns that argument.

~~~ javascript

function identify(n) {
	return function() {
		return n;
	}
}

idf = identify(3);
idf() //3
~~~

- write a function that adds from two invocations.

~~~ javascript
function addf(x) {
	return function(y) {
		return x + y;
	}
}

addf(3)(4) //7
~~~

- write a function that takes a binary function, and mkes it callable with two invocations.

~~~ javascript
function applyf(binary) {
	return function(x) {
		return function(y) {
			return binary(x,y);
		};
	};
}

addf = applyf(add);
addf(3)(4) 			// 7
applyf(mul)(5)(6)	// 30
~~~