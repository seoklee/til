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

Duck Tails

- Modules as Singletons.
	- Modules are only executed once, when first imported
- Modules initialization order is well defined. So it's very good to use as singleton.

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
>>> def color(red, green, blue, **kwargs(:
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
...			print('ocal func')
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
>>> hello('tyler')
Hello, tyler
>>> hello.count
2
~~~

Instances

- decorating with an instance calls the instance

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


