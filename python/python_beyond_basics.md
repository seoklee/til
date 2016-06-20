# Organizing Larger Programs

- Packages are generally direction
- Modules are generally files

How does python locate modules?

- sys.path
	- list of directories python searches for modules

PYTHONPATH

- environment variables listing paths added to sys.path
	- $ export PYTHONPATH=./path/path2

to create package..

- create a directory
	- and create __init__.py file

subpackages
	
- you can have subpackage inside a package. 
	- put a folder with __init__.py file in the package

relative import

- has to import something in the same moudle.
- generally avoided

~~~ python
from .a  import A
from ..b import B
~~~

__all__

- list of attribute names improted via from module import *
- The __all__ attribute should be a list of strings containing names available in the module.
- determines what names are exposed in the module

namesapce packages

- packages split across several directories
- dont have __init__.py
	- avoids complex initialization ordering problems
- extends sys.path with different directories

executable directory / zip file

~~~ python
>> python path1
ERROR because __main__.py does not exists
~~~

- directory and zip file containing an entry point for python execution
	- put __main__.py in the directory.

- Modules as Singletons.
	- Modules are only executed once, when first imported
- Modules initialization order is well defined. So it's very good to use as singleton.
s
-------

#### Function

	callable instances and the __call__() special method

Classes are callable!

~~~ python
class Resolver:
	
	def __init__(self):
		self._cache = {}
		
	def __call__(self, host):
		if host not in self_cache:
			self._cache[host] = socket.gethostbyname(host)
		return self._cache[host]
	
	def clear(self):
		self._cache.clear()
		
	def has_host(self, host):
		return host in self._acache
~~~

~~~ 
>>> from resolver import Resolver
>>> resolve = Resolve()
>>> resolve('sixty-north.com')
'98.21.123.182'
>>> from timeit import timeit
>>> timeit(setup="from __main__ import resolve", stmt="resolve('python.org')", number=1)
0.000683
>>> timeit(setup="from __main__ import resolve", stmt="resolve('python.org')", number=1)
0.000008
>>> resolve.has_host('sixty-north.com')
True
~~~

Conditional expression

- result = true_value if condition else false_value

~~~ 
>>> def sequence_class(immutable):
...		return tuple if immutable else list
>>> seq = sequence_class(immutable=True);
>>> seq('abc')
['a', 'b', 'c']
~~~

Lambdas

~~~ python
>>> scientists = ['Marie Curie', 'Albert Einstein', 'Niels Bohr', 'Issac Newton', ...]
>>> sorted(scientist, key=lambda name: name.split()[-1])
['Niels Bohr', 'Marie Curie', ...]
>>> last_name = lambda name: name_split()[-1]
>>> last_name("Nikola Tesla"]
Tesla
~~~

- zero or more arguments spported - zero arguments => lambda:
- body is a single expression
- the return value is given by the body expression
- cannot have docstrings

You can detect an object is callable or not by callable() function.

Extended formal argument syntax

- how does print function and format function can accept arbitary number of argument?

~~~ python
>>> def hypervolume(*args):
... 	print(args)
... 	print(type(args))
...
>>> hypervolume(3,4)
(3,4)
<class 'tuple>
>>> def hypervolume(*lengths):
...     i = iter(lengths)
...     v = next(i)
...     for length in i:
...             v *= length
...		  return v
...
>>> hypervolume(3, 5, 7)
105
>>> hypervolume()
StopIteration Excpetion
>>> def hypervolume(length, *lengths):
...     v = length
...     for item in lengths:
...             v *= item
...		  return v
...
>>> hypervolume(3, 5, 7)
105
>>> hypervolume()
missing 1 required positional argument
~~~

- def extended(*args, **kwargs):

~~~ python
>>> def tag(name, **attributes):
... 	result = '<' + name
... 	for key, value in attributes.items():
... 		result += ' {k]="{v}"'.format(k=key, v=str(value))
... 	result += '>'
... 	return result
...
>>> tag('img', src='monet.jpg', border='1')
'<img border="1" src="monet.jpg">
~~~

Extended actual argument syntax

~~~ python
>>> def print_args(arg1, arg2, *args):
...		print(arg1)
...		print(arg2)
... 	print(args)
>>> t = (11, 12, 13, 14)
>>> print_args(*t)
11
12
(13, 14)
>>> def color(red, green, blue, **kwargs):
...		print("r =", red)
...		print("g =", green)
...		print("b =", blue)
...		print(kwargs)
...
>>> k = {'red':21, 'green':68, 'blue':120, 'alpha':52}
>>> color(**k)
r = 21
g = 68
b = 120
{'alpha' : 52} 
~~~

Extended formal arguments have nice application

- zip function for example can have multiple arguments. you can forward the arguments easily/effecively with *args

-----

### Closures and Decorators

#### Local functions
	
- you can have a function inside of a function

~~~ python
def sort_by_last_letter(string):
	def last_letter(s):
		return s[-1]
	return sorted(strings, key=last_letter)
~~~

- remember LEGB rule!
	- in the order of local, enclosing, global, builtin

- local function is not a member. It's just local name binding in the funcion body.
	- it's useful for specialized, one-off functions
	- aid in code organization and readability
	- similiar to lambdas, but more general
		- may contain multiple expressions and multiple statments

#### Returning function

- python functions are first class funtions

~~~ python
>>> def enclosing()
... 	def local_func():
...			print('local func')
...		return local_func
...
>>> lf = enclosing()
>>> lf() 
~~~

#### Closures

- maintain references to objects from earlier scopes
	- prevent from earlier scope to be garbage collected.

~~~ python
>>> def enclosing():
... 	x = 'clsed over'
...		def local_func():
...			print(x)
...		return local_func
...
>>> lf = enclosing()
>>> lf()
closed over
>>> lf.__closure__
(<cell at ...>,,)
~~~

- closures are very useful at function factory
	- function that returns new, specialized function

~~~ python
def raise_to(exp):
	def raise_to_exp(x):
		return pow(x, exp)
	return raise_to_exp
~~~

~~~ 
>>> from raise_to import raised_to
>>> square = raise_to(2)
>>> square(5)
25
>>> square(10)
100
~~~

LEGB does not apply when making new bindings.

- nonlocal keyword
	- ####introduce names from the enclosing namespace into the local namespace

~~~ python
message = 'global'

def enclosing():
	message = 'enclosing'
	
	def local():
		nonlocal message
		message = 'local'
	
	print('enclosing message:', message)
	local()
	print('enclosing message:', message)
	
print('global message:', message)
enclosing()
print('global message:', message)
-------
global message: global
enclosing message : enclosing
enclosing message : local
global message: global
~~~

#### decorators

modify or enhance function without changing their definition

~~~ python
@my_decoration
def my_function(x, y):
	return x + y
~~~

- makes a function object by evaluating the original funciton 
	- pass the functor to the decorator and return new function object
		- then binds the original function to the returned function object

- replace, enhance, or modify existing functions
- does not change the original function definition
- calling codes does not need to change
- decorator mechanism uses the modified function's original name

~~~ python
def escape_unicode(f):
	def wrap(*args, **kwargs):
		x = f(*args, **kwargs)
		return ascii(x)
		
	# return new callable
	return wrap

@escape_unicode
def northern_city():
	return 'someUnsecpedcharacter'

---

>>> from escpae_unicode import northern_city
>>> northern_city()
~~~

Other ojects can be decorators as well.

Class

~~~ python
class MyDec:
	# will go through the constructor.
	def __init__(self, f):
		...
	
	#has to be callable! return new instance by applying class decorator
	def __call__(self):
		...
		
@MyDec
def func()
	...
~~~

Example 

~~~ python
class CallCount:
	def __init__(self, f):
		self.f = f
		self.call_count = 0
		
	def __call__(self, *args, **kwargs):
		self.count +=1
		return self.f(*args, **kwargs)

@CallCount
def hello(name):
	print('Hello, {}'.format(name))
----

>>> from call_count import hello
>>> hello('lee')
Hello, lee
>>> hello('song')
Hello, song
>>> hello.count
2
~~~

Instances

- decorating with an instance calls the instance
	- have to instantiate the object first to get the instance, and use it as the decorator

~~~ python
class AnotherDec:
	def __call__(self, f):
		def wrap():
			...
		return wrap
~~~

~~~ python
class Trace:
	def __init__(self):
		self.enabled = True
		 
	def __call__(self, f):
		def wrap(*args, **kwargs):
			if self.enabled:
				print('calling {}'.format(f))
			return f(*args, **kwargs)
		return wrap
		
tracer = Trace()
 
@tracer
def rotate_list(l):
	return l[1:] + [l[0]]

---

>>> from tracer import rotate_list, tacer
>>> l = [1, 2, 3]
>>> l = rotate_list(l)
Calling <function ...>
>>> l
[2, 3, 1]
>>> l = rotate_list(l)
Calling <function ...>
>>> l
[3, 1, 2]
>>> tracer.enable = False
>>> l = rotate_list(l)
[3, 1, 2]
~~~

All of those methods are valid. But you have to know which method is useful on which occasion. Pracice!

Multiple decorators can be used. 

@decorator3
@decorator2
@decorator1

decorator 1,2,3 will be used in that order.

You can definitely decorate a method.

Naive decorator can lose important metadata.

~~~ python
def hello():
	"print a well-known message."
	print ("hello world!")

--- 
>>> from noop import hello
>>> hello.__name__
'hello'
>>> hello.__doc__
"print a well-known message."
~~~

Makes sense so far.

~~~ python
noop.py

def noop(f):
	def noop_wrapper():
		return f()
	return noop_wrapper

@noop
def hello():
	"print a well-known message."
	print ("hello world!")
---
--- 
>>> from noop import hello
>>> hello.__name__
'noop_wrppaer'
>>> hello.__doc__
''
~~~

Oh no. This behavior is probably not intended.
We can easily fix this, through functools.wrpas()

- properly update metadata on wrapped functions

~~~ python
noop.py

import functools

def noop(f):
	@functools.wraps(f)
	def noop_wrapper():
		return f()
	return noop_wrapper

@noop
def hello():
	"print a well-known message."
	print ("hello world!")
---
--- 
>>> from noop import hello
>>> hello.__name__
'hello'
>>> hello.__doc__
'print a well-known message.'
~~~

Decorator is really useful for validator!

~~~ python
def check_non_negative(index):
	def validator(f):
		def wrap(*args):
			if args[index] < 0:
				raise ValueError(
					'Argument {} must be non-negative.'.format(index))
			return f(*args)
		return wrap
	return validator

@check_non_negative(1)
def create_list(value, size):
	return [value] * size
~~~

Whoa. check_non_negative is not a decorator, because it's not callable.
But by supplying an argument, you return a decorator, which is named validator. (using closure)

----

###Properties and Class methods

class attributes vs instance attributes

- instance attributes assigned to per object basis. (usually in __init__ method)
- class attribute is associated with a whole class
	- created in the class scope

~~~ python
shipping.py

class ShippingContainer:

	next_serial = 1337

	def __init__(self, owner_code, contents):
		self.owner_mode = owner_code
		self.contents = contents
		self.serial = next_serial
		next_serial += 1
~~~

~~~
>>> from shipping import *
>>> c3 = ShippingContainer("MAE", "tools")
UnboundLocalError : local variable next serial... blahblah
~~~

We have to therefore change it to

~~~ python
shipping.py

class ShippingContainer:

	next_serial = 1337

	def __init__(self, owner_code, contents):
		self.owner_mode = owner_code
		self.contents = contents
		self.serial = ShippingContainer.next_serial
		ShippingContainer.next_serial += 1
~~~

~~~
>>> from shipping import *
>>> c3 = ShippingContainer("MAE", "tools")
>>> c3.serial
1337
>>> c4 = ShippingContainer("ESC", "noodles")
>>> c4.serial
1338
~~~

You could have written...

~~~ python
shipping.py

class ShippingContainer:

	next_serial = 1337

	def __init__(self, owner_code, contents):
		self.owner_mode = owner_code
		self.contents = contents
		self.serial = self.next_serial
		self.next_serial += 1
~~~


- avoid this because
	- readability issue using self. Cannot distinguish whether this is class attributes or object attributes
- Also an undesirable side effect
	-  you are creating an instance attribute, rather than changing the class attribute.
	

####static methods

get rid of self in the static method.

~~~ python
shipping.py

class ShippingContainer:

	next_serial = 1337
	
	@staticmethod
	def __get__next__serial():
		result = ShippingContainer.next_serial
		ShippingContainer.next_serial += 1
		return result

	def __init__(self, owner_code, contents):
		self.owner_mode = owner_code
		self.contents = contents
		self.serial = ShippingContainer._get_next_serial()
~~~

####class method

accept cls as the first argument. It's analoguous to self in a way, but you get referecne to the class.

~~~ python
shipping.py

class ShippingContainer:

	next_serial = 1337
	
	@classmethod
	def __get__next__serial(cls):
		result = cls.next_serial
		cls.next_serial += 1
		return result

	def __init__(self, owner_code, contents):
		self.owner_mode = owner_code
		self.contents = contents
		self.serial = ShippingContainer._get_next_serial()
~~~

If you need to access to class attribute, use classmethod. If not use staticmethod.

- staticmethod will mostly be implementation detail of the class
	- may be able to moved to become a module-scope function

class methods for named constructors

- kinda like factory!

~~~ python
shipping.py

class ShippingContainer:

	next_serial = 1337
	
	@staticmethod
	def _make_bic_code(owner_code, serial):
		return iso6346.create(owner_code=owner_code,
								  serial=str(serial).zfill(6))
	
	@classmethod
	def __get__next__serial(cls):
		result = cls.next_serial
		cls.next_serial += 1
		return result

	@classmethod
	def create_empty(cls, owner_code):
		return cls(owner_code, contents=None)

	def __init__(self, owner_code, contents):
		self.owner_mode = owner_code
		self.contents = contents
		self.bic = ShippingContainer._make_bic_code(
			owner_code=owner_code,
			serial=ShippingContainer._get_next_serial())
~~~

static methods with inheritance

static methods can be overridden in the subclass.

~~~ python
class RefrigeratedShippingContainer(ShippingContainer):

	@staticmethod
	def _make_bic_code(owner_code, serial):
		return iso6346.create(owner_code=owner_code,
								  serial=str(serial).zfill(6),
								  category='R')
~~~

~~~
>>> r1 = RefrigeratedShippingContainer("MAE", 'fish)
>>> r1.bic
MAEU0013371
~~~

Does not work, because it uses the ShippingContainer's class attribute, the static method of _make_bic_code(..) in the __init__()

You get a polymorphic static method only through the instance, not through the class with the code written above. You have to use self instnance in this case.


~~~ python
shipping.py

class ShippingContainer:

	next_serial = 1337
	
	@staticmethod
	def _make_bic_code(owner_code, serial):
		return iso6346.create(owner_code=owner_code,
								  serial=str(serial).zfill(6))
	
	@classmethod
	def __get__next__serial(cls):
		result = cls.next_serial
		cls.next_serial += 1
		return result

	@classmethod
	def create_empty(cls, owner_code):
		return cls(owner_code, contents=None)

	def __init__(self, owner_code, contents):
		self.owner_mode = owner_code
		self.contents = contents
		self.bic = self._make_bic_code(
			owner_code=owner_code,
			serial=slef._get_next_serial())
			
class RefrigeratedShippingContainer(ShippingContainer):

	@staticmethod
	def _make_bic_code(owner_code, serial):
		return iso6346.create(owner_code=owner_code,
								  serial=str(serial).zfill(6),
								  category='R')
~~~

class methods with inheritance.

- acts as expected. (polymorphically b/c __init__ is inherited)

~~~ python
>>> r1 = RefrigeratedShippingContainer.create_empty("YML")
>>> r1
<shipping.RefrigeratedShippingContainer at ...>
~~~

overriden __init__() in the subclasses 

- calls base classes' __init__() forwarding the own code and contents arguments to the base class initalizer.
	- you should be explicit about calling the base class' __init__ by calling super()

~~~ python
#modified class
			
class RefrigeratedShippingContainer(ShippingContainer):
	
	MAX_CELCIUS = 4.0
	
	@staticmethod
	def _make_bic_code(owner_code, serial):
		return iso6346.create(owner_code=owner_code,
								  serial=str(serial).zfill(6),
								  category='R')
	
	def __init__(self, owner_code, contents, celcius):
		super.__init__(owner_code, contents)
		if celcis > RefrigeratedShippingContainer.MAX_CELCIUS:
			raise ValueError("Temperature too hot!")
		self.celcius = celcius
~~~

~~~
>>> from shipping import *
>>> r3 = RefrigeratedShippingContainer.create_with_items('ESC')

ERROR __init__ missing 1 positional argument : 'celcius'
~~~

This sounds right. Base class does not know about extra argument in the derived class. Here is a workaround by fowarding the arguments using extended formal arguments.

~~~ python

class ShippingContainer:
 	...
 
 	@classmethod
 	def create_empty(cls, owner_code, *args, **kargs):
 		return cls(owner_code, content=None *args, **args)
~~~

~~~
>>> from shipping import *
>>> r3 = RefrigeratedShippingContainer.create_with_items('ESC', celsius=2.0) 
>>> r3.celsius = 12.0
~~~

This is bad. This should raise an error!

using validation using set_celsius would be deeply unpythonic. Use @property decorator

####Encapsulation using @property decorator 


~~~ python
#modified class, renaming the celsius w/ _celsius.
			
class RefrigeratedShippingContainer(ShippingContainer):
	
	MAX_CELCIUS = 4.0
	
	@staticmethod
	def _make_bic_code(owner_code, serial):
		return iso6346.create(owner_code=owner_code,
								  serial=str(serial).zfill(6),
								  category='R')
	
	def __init__(self, owner_code, contents, celcius):
		super().__init__(owner_code, contents)
		if celcis > RefrigeratedShippingContainer.MAX_CELCIUS:
			raise ValueError("Temperature too hot!")
		self._celcius = celcius
		
	@property
	def celsius(self):
		return self._celsius
~~~

Property decorator convert the function to where it acts like an attribute

~~~ 
>>> r3 = RefrigeratedShippingContainer.create_with_items('ESC', celsius=-12.0) 
>>> r4.celsius
-12
>>> r4.celsius = -5.0
ERROR
~~~

in order to attribute assignment to work. need to define setter using another decorator

- decorator : have original function object that gets wrapped by the decorator.

setter also needs to be decorated. Rather than using a built-in decorator, use a decorator that's specific to this property, which is a self attribute to the property object that was created when we defined getter.

~~~ python
class Example:

	@property
	def p(self):
		return self._p

	@p.setter
	def p(self, value):
		self._p = value

~~~


~~~ python
#coming back
			
class RefrigeratedShippingContainer(ShippingContainer):
	
	MAX_CELCIUS = 4.0
	
	@staticmethod
	def _make_bic_code(owner_code, serial):
		return iso6346.create(owner_code=owner_code,
								  serial=str(serial).zfill(6),
								  category='R')
	
	def __init__(self, owner_code, contents, celcius):
		super().__init__(owner_code, contents)
		if celcis > RefrigeratedShippingContainer.MAX_CELCIUS:
			raise ValueError("Temperature too hot!")
		self._celcius = celcius
		
	@property
	def celsius(self):
		return self._celsius
		
	#celsius is decorated with property. 
	#returned object is also bound by celsius which has etter decorator
	@celsius.setter
	def celsius(self, value):
		if value > RefrigeratedShippingContainer.MAX_CELSIUS:
			raise ValueError("Temperature too hot!")
		self._celsius = value	
~~~

Adding Farenheit support

~~~

class RefrigeratedShippingContainer(ShippingContainer):
	...
	
	def __init__(self, owner_code, contents, celcius):
		super().__init__(owner_code, contents)
		self._celcius = celcius
	
	@staticmethod
	def _c_to_f(celsius):
		return celsius * 9/5 + 32
	
	@staticmethod
	def _f_to_c(fahrenheit):
		return (fahrenheit - 32) * 5/9
	
	@property
	def fahrenheit(self):
		return RefrigeratedShippingContainer._c_to_f(self.celsius)
		
	# reusing validation.
	@fahrenheit.setter
	def fahrenheit(self, value):
		self.celsius = RefrigeratedShippingContainer._f_to_c(self.celsius)
~~~

#### inheritance with @property decorator

- Inheritance work as it is. Declared in base class, you can use it in the subclass.
	- overriding is also pretty easy. just redefine it in the subclass
		- you can reduce the code, by using super.

~~~ python
# at base class
@property 
def volume_ft3(self):
	return HIGHT_FT * WIDTH_FT * self.length
	
#at subclass
@property
def volume_ft3(self)
	return super().volume_ft3 - RefrigeratedShippingContainer.FRIDGE_VOLUME_FT3
~~~

- overriding setter is hard though.
	- The code below does not work, because it tries to get celsius object from where setter decorator comes from is not visible in that scope.

~~~ python

class HeatedRefrigeratedShippingContainer(RefigeratedShippingContainer):

	MIN_CELSIUS = -20.0
	
	@celsius.setter
	def celsius(self, value):
		...  
~~~

You have to fix it by providing the whole base classes name. 
Trying to reduce the code by calling super() and get its setter function would not work. You have to call base class' property function

~~~ python

class HeatedRefrigeratedShippingContainer(RefigeratedShippingContainer):

	MIN_CELSIUS = -20.0
	
	@RefigeratedShippingContainer.celsius.setter
	def celsius(self, value):
		if value < HeatedRefrigeratedShippingContainer.MIN_CELSIUS:
			raise valueError("Temperature too cold!")
		# This does not work
		#super().celsius = value
		RefigeratedShippingContainer.celsius.fset(self, value)
~~~


#### Template method

Implement skeletal operation in base classes, defer some details to subclasses.

You can force the abstraction by raising an error in the base class Now subclass has to implement it to avoid the error.

What you can do, then. You can make volume_ft3 to return a return-val from a function called _calc_volume(). (with @property) Then _calc_volume can be overriden elsewhere


------

# Strings and Representation

str() and repr() are functions for making string representations from python objects which call the methods __str__() and __repr__()

repr()

- produces an unambiguous string representation of an object
	- type of the object and all the attributes
	- some suggest that it would be almost like a constructor
	- should generally contain more info than the result of str
		- str for the clients
		- repr for the developers
	- will be used at PDB or other debugger
	- always write a repr() for your classes

~~~ 
>>> p = Point2D(x=12, y=15)
>>> repr(p)
Point2D(x=12, y=15)
~~~

str()

- produces a readable, human-friendly representation of an object

~~~
>>> p = Point2D(123, 456)
>>> str(p)
(123, 456)
~~~

By default, str() function will call repr() if __str__() is not defined. But it's not true for reverse. 

repr() is used when showing elements of a collection

format method will call __format__() of that object.

- f is for optional format specification after colon 
	- {field_name:format_spec}
	- use str() for default
	- if you want to explicitly call repr(), you have to do {!r} in format's string argument, and {!s} to call str()

~~~ python
class Something:
	...
	
	def __format__(self, f):
		if f=='r':
			return'{}, {}'.format(self.y, self.x)
		else:
			return'{}, {}'.format(self.x, self.y)
~~~

reprlib offers a good, useful, alternate to repr()

####built-in functions

ascii()

- replaces non-ASCII characters with escape sequences

ord() & chr()

- ord() convert a single character to its Integer Unicode codepoint
- chr() converts an integer Unicode codepoint to a single character.



---

#Numeric and Scalar Types

###int

- unlimited precision signed integer

###float

- 64 bit double precision, aka double
	- 53 bits of binary precision
- 15 to 17 bits of decimal precision

conversion to int to float and vice versa can result in loss of data. Some fraction cannot be represented perfectly, too.

###decimal module contains Decimal class

- decimal floating point
- configurable (although finite) precision
- defaults 28 digits of decimal precision

Always quote literal fractional value.
~~~ python
>>> from decimal import Decimal
>>> Decimal(7)
Decimal('7')
>>> Decimal('0.8')
Decimal('0.8')
>>> Decimal('0.8') - Decimal('0.7')
Decimal('0.1')
>>> Deciamal(0.8) - Decimal(0.7)
0.09999989898989
~~~

It's because binary, inaccurate number are used for floats without quotes

decimal.getcontext().traps[decimal.FloatingOperation] = True

- prevents any float operation

decimal.getcontext().prec

- significant figure

Different modulus & division operation for different number type.

- be careful!

###Fraction

fracion module containing the class Fraction

~~~ python
>>> from fractions import Fraction
>>> two_thirds = Fraction(2, 3)
>>> four_fifths = Fraction(4, 5)
>>> Fraction('0.1')
>>> Fraction(Decimal('0.1'))
>>> floor(Fraction('4/3'))
1
~~~

### Complex Number

### Base Conversion

~~~ python
>>> 0b101010
42
>>> 0o52
42
>>> ox2a
42
>>> hex(42)
'0x2a'
>>> hex(42)[2:]
'2a'
>>> int("2a", base = 16)
42
>>> int("0b111000", base=2)
56
~~~

### Date and Time

date

- year
- month
- day

time

- hour
- minute
- second
- microsecond

datetime

- date
	- year
	- month
	- day
- time 
	- hour
	- minute
	- second
	- microsecond

isoweek() starts at 1 : Monday, 2 : Tuesday ...

weekday() strat at 0 : Monday, 1 : Tuesday


~~~ python
>>> import datetime
>>> datetime.date(2014, 1, 6)
datetime.date(2014, 1, 6)
>>> datetime.date(year=2014, month=1, day=6)
datetime.date(2014, 1, 6)
>>> d = datetime.date.today()
datetime.date(2014,2, 27)
>>> d.year
2014
>>> d.weekday()
3
>>> d.isoweekday()
4
>>> d.isoformat()
2014-02-27
~~~

~~~ python
d.strfttime()

>>> d.strfttime('%A %d %B %Y')
'Thursday 27 February 2015'
>>> "{date:%A} {date.day} {date:%B} {date.year}".format(date=d)
'Monday 6 January 2015'
~~~

~~~ python
>>> t = datetime.time(hour=23, minute=59, second=59, microsecond=99999)
>>> t.hour
23
>>> t.microsecon
99999 
>>> "{t.hour}h{t.minute}m{t.second}s".format(t=t)
'23h59m59s'
~~~

~~~ python
>>> import datetime as dt
>>> dt.datetime(2003, 5, 12, 14, 33, 22, 245323)
datetime.datetime(2003, 5, 12, 14, 33, 22 , 245323) 
>>> d = dt.date.today()
>>> t = dt.time(8, 15)
>>> dt.datetime.combine(d,t)
dt.datetime(2014, 2, 27, 8, 15)
~~~

... etc
---

### Iterables and Iterations

comprehension

- short-hand syntax for creating collections and iterable objects
- can use multiple input sequences and multiple if clauses

~~~ python
[(x, y) for x in range(5) for y in range(3)]

# pretty much equivalent to...
>>> opints = []
>>> for x in range(5):
... 	for y in range(3):
...			points.append((x, y))
~~~

~~~ python
values = [x / (x - y) 
			for x in range(100)
			if x > 50
			for y in range(100)
			if x - y != 0] 

#equivalent
values = []
for x in range(100): 	
	if x > 50:
		for y in range(100):
			if x - y != 0:
				values.append(x / (x - y))
~~~

Comprehension can be nested inside other comprehension

~~~ python
vals = [[y * 3 for y in range(x)] for x in range(10)]

outer = []
for x in range(10):
	inner = [] 
	for y in range(x):
		inner.append(y * 3)
	outer.append(inner)
~~~

~~~ python
>>> {x * y for x in range (10) for y in range(10)}
>>> g = ((x * y) for x in range(10) for y in range(x))
>>> type(g)
<class 'generator'>
~~~

map()

- apply a function to every element in a sequence, producing a new sequence
- map is lazy - it only produces values as they're needed
- can accept any number of input sequences
- the number of input sequence must match the number of function arguments
-  map(f, a, b, c)
	- f(a, b, c) -> output
	- will terminate when any of yhe sequence terminate.

either map() or comprehension is better than one another if the result is the same

filer()

- filer(is_odd, [1, 2, 3, 4, 5]);

~~~ python
>>> positives = filter(lambda x: x > 0, [1, -5, 0, 6, -2, 8])
>>> list(positives)
[1, 6, 8] 
~~~

- passing None as the first argument will filter out any false-y values

#### in python 2 actually eagerly evaluate map and filter

functools.reduce() (aka fold and accumulate)

- repeatedly apply a function to the elements of a sequence, reducing them to a single value.

~~~ python
>>> from functools import reduce
>>> import operator
>>> reduce(operator.add, [1,2,3,4,5])
15
~~~

- if seqeunce length is one, the value just returns
- optional initial value is conceptually just added to the start of the input sequence

~~~ python
>>> values = [1, 2, 3]
>>> reduce(operator.add, values, 0)
6
>>> values = []
>>> reduce(operator.add, values, 0) #avoids error
0
>>> values = [1, 2, 3]
>>> reduce(operator.add, values, 0)
0
~~~

Iteration

- iters()
	- create an iterator
- next()
	- get next element in sequence
- StopIteration
	- signal the end of the sequence

iterable

- an object which implements the __iter__() method
- The alternative iterable protocol works with any object that support consecutive integer indexing via __getitem__()

iterator

- an object which implements the iterable protocol
- and which implements the __next__() method


Extended form of iter

iter(callable, sentinel)

- callable : callable object that takes zero arguments
- sentinel : iterations stops when callable produces this value
- often used for creating infinite sequences from existing functions
	- ex: 
		- read a file until you see a specific line.

---

### Inheritance

- subclasses will want to initialize base classes
- base class initializer will only be called automatically if subclass initializer is undefined. 
- super() to used access base-class implementation

isinstance()

- determines if an object is of a specified type

~~~ python
>>> isinstance(3, int)
True
>>> x = []
>>> isinstance(3, (float, list, int))
>>> True
~~~

issubclass()

- determines if one type is a subclass of another

multiple inheritance

- defining a class with more than one base class

~~~ python
class SubClasss(base1, base2 ...)
~~~

- without conflict, names resolve in the obvious way.
- Method Resolution Order determines name lookup in all cases

if a class...

- has multiple base classes
- defines no initlizer

then only the initalizer of the first base class is automatically called.

__bases__ 

- a tuple of base classes

method resolution order (MRO)

- ordering that determines method name lookup
- methods may be defined in multple places
- ordering of the inhertiance graph
- __mro__ (as tuple) mro() (as list)
	- check the order of the method in the mro. If there is, you call that method
	 
C3 algorithm for calculating MRO in Python

- subclasses come before base classes
- base class order from class definition is preserved
- first two qualities are preserved no matter where you start in the inheritance graph

~~~ python
class A:

class B(A):
	pass

class C(A):
	pass

class D(B, A, C):
	pass
	
ERROR
~~~

super()

- given a class resolution order and a class C, super() gives you an object which resolves methods using only the part of the MRO which comes after C
- returns a proxy object which routes method calls
	- bound proxy
		- bound to a specific class or instance
		- two types of bound proxies
			- instance-bound
				- super(class, instance-of-class)
					- finds the MRO for the type of the second argument
					- finds the location of the first argument in the MRO
					- uses everything after that for resolving methods 
			- class-bound
				- super(base-calss, derived-class)
					- python finds MRO for derived-class
					- then finds base-class in that MRO
					- it takes everything after base-class in the MRO, and finds the first class in that sequence with a matching method name
	- unbound proxy
		- not bound to a class or instance
- without argument?
	- instance method
		- super(class-of-method, self)
	- class method
		- super(class-of-method, class)