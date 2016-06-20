# Python Fundamentals


- REPL
	- Read-Eval-Print-Loop
	- python command line read, eval, print, and loop back
	- underscore (_) is useful in python command line.

- Space
	- 4 spaces instead of tabs
	- NEVER mix spaces and tabs

- PEP 8 (Python Style Guide)
	- read it and do it.

- PEP 20 (Zen of Python)
	- live it

- import __
	- importing a library
- from __ import ____
	- import a certain module in a library
- from __ import ____ as ______
	- import a certain module in a library as this name

- // or **
	- integer operations

- scalar types
	- int
		- unlimited precision signed integer
		- int("10000", 3)
			- convert to base 3
	- float
		- 53 bits of binary precision
		- 15 to 16 bits of decimal precisioner
		- 
		- anything with . or e
			- 3e8 or 1.611e-35
		- any operation with int and float will coerce the result to float
	- None
		- abseence of a value
		- nothing displayed @ REPL
	- bool
		- either True or False
			- only 0 is false-y. (Even negative numbers are true-y)
			- only empty list or string are false-y.

- string
	- str, immutable sequence of Unicode codepoint
		- '' for literal
		- BE CONSISTENT.
	- multi line string using leading / terminating """ 
	- or embed newline charact
	- escaping a character using \.
	- raw string does not care about escaping
		- r'kasldfjklas\dsfas\...' 
	- uniform encoding : UTF-8

- byte
	- immutable seqeunce of bytes.
		- ascii-like.
	- byte literal
		- b'data' 
	- need to decode bytes into the string, and encode str to bytes

- list
	- mutable seqeunces of objects

------

- import __
	- omit the .py extension
	
- function
	- implicit return or return without anything will return none

random.py 

~~~ python
	def hello_world():
		print "Hello World"
~~~
~~~
$ python3
>>> import random
>>> random.hello_world()
Hello World
~~~

Or...

~~~
$ python3
>>> from random import hello_world 
>>> hello_world()
Hello World
~~~
	
Notice that you could only run it in REPL. Nothing happens if you run below command.

~~~
$ python3 random
~~~	

What you can do is, you can check the __name__ of the module. Check it out.

random.py 

~~~ python
	def hello_world():
		print ("Hello World")
		
	print (__name__)
~~~

~~~ python
$ pyton3
>>> import words
words
>>> import words

ctrl + d
 
$ python3 random.py
__main__
~~~

So now, you can do something like this.	

~~~ python
	def hello_world():
		print ("Hello World")
		
	if __name__ == '__main__':
		hello_world()
~~~
	
Python module

- convenient import with API

Python script

- convenient execution from command line

Python program

- Perhaps composed of many moudle

You should always combine module and script together.

- make script importable, and vice versa

---------

example code w/ command line argument

~~~python
from urllib.request import urlopen
	

def fetch_word(url):
	with urlopen('url') as story:
		story_word = []
		for line in story:
			line_words = line.decode('utf-8').split()
			for word in line_words:
				story_word.append(word)
	return story_word
		

def print_items(items):
	for item in items  :
		print(item)

		
def main():
	url = sys.argv[1]
	items = fetch_word(url)
	print_word(items)


if __name__ == '__main__':
	main()
~~~

But wait, but you probably cannot use this in REPL without argument.

~~~
>>> from example import main()
>>> main()
ERROR
~~~

Fix below.

~~~python
from urllib.request import urlopen
	

def fetch_word(url):
	with urlopen('url') as story:
		story_word = []
		for line in story:
			line_words = line.decode('utf-8').split()
			for word in line_words:
				story_word.append(word)
	return story_word
		

def print_items(items):
	for item in items  :
		print(item)

		
def main(url):
	items = fetch_word(url)
	print_word(items)


if __name__ == '__main__':
	main(sys.argv[1])
~~~


~~~
>>> from example import main()
>>> main("bakfjklsdfjk.com")
GOOD
~~~

------

For Documentation, Follow Google's Python style guide with docstring. If you follow it, you can use help(funtion_name) and documentation will show up!

------
Shebang

- #!/usr/bin/env python3
	- user does not have to specify "python3 __.py"
	- don't forget chmod!

----

Python objects are immutable

~~~
//points to 1000 object
x = 1000
// points to 500 object and 1000 object will be garbage collected
x = 500 
~~~

id function
 
- returns unique identifier for each object

is operator

- tests two references refers to the same object

LIST IS MUTABLE.

No variables. Only names references to objects

~~~ 
>>> p = [4, 5]
>>> q = [4, 5]
>>> p == q
True
>>> p is q
False
~~~

This makes sense. g and f pointed to the same thing at one point. But it points to something else in the function. The value of the reference is copied, not the value of the object.

~~~ python
>>> f = [14, 23, 37]
>>> def replace(g):
...  	g = [17, 28, 45]
... 	print("g = ", g)
... 
>>> replace(f)
g = [17, 28, 45]
>>> f
[14, 23, 37]
~~~

####Default argument values are evaluated when def is evaluated. They can be modified linke any other object.

Always use immutable object for default argument

~~~ python

def add_spam(menu=[]):
	menu.append("spam")
	return menu

>>> add_spam()
[spam]
>>> add_spam()
[spam, spam]
...

def add_spam(menu=None):
	if menu is None:
		menu = []
	menu.append('spam')
	return menu

>>> add_spam()
[spam]
>>> add_spam()
[spam]

~~~

object references have no type.

Scopes are contexts in which named reference can be looked up

- local
- enclosing
	- any and all enclosing function
- global
- built-in
	- provided by the builtins module

LEGB rule


~~~python 
count = 0

def show_count():
    print("count : ", count)
    

def set_count(c):
	count = c
~~~
~~~
>>> show_count()
0
>>> set_count(5)
>>> show_count()
0
~~~

Makes sense. new varaible called count is created at set_count. How do we fix it with global variable?


~~~python 
count = 0

def show_count():
    print("count : ", count)
    

def set_count(c):
	global count
	count = c
~~~
~~~
>>> show_count()
0
>>> set_count(5)
>>> show_count()
5
~~~

--------

Collections

tuple

- immutable list
	- ( ) instead of [ ] of the list
- concatenation with + operator
- repetition with * operator
- can't make tuple with a single object.
	- have to have trailing ,
- to make empty tuple, just use ()

~~~ python
>>> h = (123)
>>> type(h)
<class 'int'>
>>> h = (123,)
>>> h
(123,)
~~~

- tuple unpacking is nice.

~~~python
def min_max(arr):
	return min(arr), max(arr)

>>> min_max([12, 98 23])
(12, 98)
>>> lower, upper =  min_max([12, 98 23])
~~~

- you can make tuple out of iterable using tuple constructor
- in and not in operator

str

- homogeneous immtable sequence of unicode
- join function to "join" the iterable with connector

~~~ python
>>> abc = [a, b, c]
>>> ''.join(abc)
abc
~~~

- you can also split the string to iterable with delimitor 
- partition methods divides a string into three around a separator: prefix, separator, suffix

~~~ python
>>> "unforgetable".partition("forget")
('un', 'forget', 'able')
>>> departure, _, arrival = "London:Edinburgh".partition(:)
>>> departure
London
>>> arrival
Edinburgh
~~~

- Format is a nice function. Use it!

~~~ python
>>> "the age of {0} is {1}".format('Jim', 32)
'the age of jim is 32'

>>> "the age of {} is {}".format('Jim', 32)
'the age of jim is 32'

>>> "the age of {name} is {age}".format('name='Jim', age=32)
'the age of jim is 32'

>>> pos = (12.4, 98.1, 82.5)
>>> "Galactic position x={pos[0]} y={pos[1]} z={pos[2]}".format(pos=pos)
"Galactic position x=12.4 y=98.1 z=82.5"

>>> import math
>>> "Math constants: pi={m.pi}, e={m.e}".format(m=math)
'Math constants: pi=3.1459..., e=2.718...
>>> "Math constants: pi={m.pi:.3f}, e={m.e:3f}".format(m=math)
'Math constants: pi=3.142, e=2.718
~~~

range

- arithmetic progrssion of integer
- you can list-ify a range! 
- don't use range to iterate list
	- unpythonic
- use enumerate for needing a counter!

list

- slicing. s[start:stop]
- to copy list, use s[:] by giving full list. 
	- or... s.copy()
	- or... list(s)
- be careful! copies are shallow

~~~ python
>>> a = [ [1, 2], [3, 4] ]
>>> b = a[:]
>>> a is b
False
>>> a == b
>>> a[0]
[1,2]
>>> b[0]
[1,2]
>>> a[0] = [8,9]
>>> a[0]
[8,9]
>>> b[0]
[1,2]
>>> a[1].append(100)
>>> a[1]
[3,4, 100]
>>> b[1]
[3,4]
~~~

- repetition w/ * operator
	- repetitiion is SHALLOW

~~~ python
>>> l = [ [1, 2, 3] ]
>>> l = l * 5
>>> l[0].append(4)
>>> l
[ [1, 2, 3, 4], [1, 2, 3, 4], [1, 2, 3, 4], [1, 2, 3, 4], [1, 2, 3, 4] ]
~~~

- index will find the value in the array and return the index in the arr
- in and not in also available
- del to remove the index and renumber the element
	- remove to remove the first matching values
- insert(index, item)
- concatenate list + operator will create a new list
- concatenate list += operator or extend() will do it inplace
- sort() & reverse
	- you can also put key to sort it in some standard
- sorted() built in function sorts any iterable series and returns a list
- reversed() built-in function reverse any iterable series and return a reverse iterator. (list-ify afterward!)


dict

- {}
- keys must be immutable 
- value can be mutable
- dictionary itself is mutable
- arbitary order
- dict() constructor accepts
	- iterable series of key-value 2-tuples
	- keyword arguments - requires keys are valid Python identifiers
	- a mapping, such as another dict
- dict copying is shallow in default
	- .copy() for copying
	- extends dictionary with update()
		- target will replace the source on update()
- values() for iterable views onto the series of values
- items for iterable view onto series of key-value tuples
- in & not in operator for keys
- del d[key] for deleting the entry
- pprint (pretty printing)
	- python standard library pprint module

~~~ python
>>> from pprint import pprint as pp
>>> pp(d)
{
... 
...
...
}
~~~ 

set

- itself is mutable
- objects itself must be mutable
- comma separated with {} or set() constructor
- duplicates are discarded
- in & not int operator
- add method to add stuff
- update to do multiple addition
- remove raises KeyError if not exists 
	- discard always succeeds
- copy() or copy by constructor
	- copies are shallow
- set algebra
	- union() opeartion
	- intersection() operation
	- difference() 
	- symmetric_difference(t)
		- everything except intersection
	- issubset()
	- issuperset()
	- isdisjoint()
		- no member in common

Protocol

- to implement protocol, must support certain operations
	- most collections implement container, sized, and iterable
		-  container : in & not int
		-  sized : the
		-  iterable : can produce iterator with iter(s)
		-  sequence : retrieve elements by index. find items by value. count items. produce a reversed sequence
- all except set and dict are sequences

----

Exception

~~~ python
def convert(s):
	x = int(s)
	return x
~~~

~~~ python
>>> convert(2);
2
>>> convert("123")
123
>>> convert("asldkfjsdlk")
valueError
~~~

~~~ python
def convert(s):
	try:
		x = int(s)
		print("converstion successful! x =", x);
	except ValueError:
		print("conversion failed")
		x = -1	
	return x
~~~

You can specify multiple errors with tuples of errors

~~~ python
def convert(s):
	try:
		x = int(s)
		print("converstion successful! x =", x);
	except (ValueError, TypeError):
		x = -1	
	return x
~~~

python will throw an error if exception block is empty. use pass then or return the value immediately

you can also scrutinize error with...

~~~ python
def convert(s):
	try:
		x = int(s)
	except (ValueError, TypeError) as e
		print("conversion error: {}" \
			.format(str(e)),
			file=sys.stderr)
	return -1
~~~

you can slso re-reaise exception inside except block using raise.

- indexError
	- duh
- valueError
	- object is of the right type, but wrong value
- keyError
	- lookup fails

Avoid protecting against type error.

Look Before You Leap vs It's Easier to Ask Forgiveness than Permission
 - Python prefers EAFP

try... finally will allow you to clean-up.

errors should never pass silently, except silented explicitely.
	
-----

###Iterable

List

~~~ python
>>> words = ["abc", "defwer", ...]

// [len(word) for word in words]
// [expr(item) for item in iterable]
~~~

Set

~~~ python
>>> { len(str(factorial(x))) for x in range(20}
// no meaningful order, since it's unordered.
~~~

dict

- later keys replace earlier keys

~~~ python
>>> country_to_capital = {'United Kingdom' : London,
								'Brazil' : 'Brazilia',
								... }
>>> capital_to_country = {capital: country for country, capital in country_to_capital.item()}
{ 'Brazilia' : 'Brazil',
  'London' : 'United Kingom',
  ...}
~~~

Optional filtering clause

- [expr(item) for item in iterable if predicate(item) ]
	- predicate returning True or False

Iteration protocol

- iterable protocol
	- iterable objects passed to the built-in iter() function to get an iterator

- iterator protocol
	- iterator objects can be passed to the built-in next() function to fetchthe next item.

~~~ python
>>> iterable = ['Spring', 'Summer', 'Autumn', 'Winter']
>>> iterator = iter(iterable)
>>> next(iterator)
'Spring'
>>> next(iterator)
'Summer'
>>> next(iterator)
'Autumn'
>>> next(iterator)
'Winter'
>>> next(iterator)
StopIteration exception
~~~

Seems weird that it raises an exception. But the language design allowed it. Detect infinite loop.

generator

- specify iterable sequences
- lazily evaluated
	- next value in the sequence is computed on demand
- cam model infinite sequence
	- streams of data with no definite end
- are composable into pipelines
	- natural stream processing
- next will run upto the first yield... and onto the next
- during for loop StopIteration is caught

~~~ python
>>> def gen123():
... 	yield 1
... 	yield 2
... 	yield 3
...
>>> g = gen123()
>>> g
<generator object gen123 at xxxxx >
>>> next(g)
1
>>> next(g)
2
>>> next(g)
3
>>> next(g)
StopIteration exception
>>> for v in gen123():
...		print(v)
...
1
2
3
~~~

generator comprehension

- generator is one time use object 

~~~ python
>>> million_squares = (x*x for x in range(1, 1000001))
>>> million_squares
<generator object>
>>> list(million_sqaures)
gozillion numbers
>>> list(million_squares)
[]
~~~

itertools

- islice
	- elements from seq
- count
	- count from the start
- any
	- returns True if any of them are True
- all
	- returns True if all of them are True
- zip
	- zip iterables and return tuple of ith element.
- chain
	- you can view all of the iterable w/ chain without creating new list.


~~~ python
>>> from itertools import islice, count
>>> islice(all_primes, 1000)
>>> 
>>> thousand_primes = islice((x for x in count() if is_prime(x)), 1000)
>>> 
~~~

-------

### class

- class names are CamelCase

~~~ python
// airtravel.py

class Flight:
	pass

>>> from airtravel import Flight
>>> f = Flight()
>>> type(f)
<class airtravel.Flight>
~~~

- Method
	- a function defined within a class
- Instance methods 
	- functions which can be called on objects.
- self is the first argument of all.


- __init__()
	- instance method for initializing objects
	- not a constructor, but an initializer.
	- self is similar to this in c++ or java

~~~ python
// airtravel.py

class Flight:

		__init__(self, number):
		self._number = number
	
	number(self):
		return self._number
~~~
	
- _number?
	- avoid name clash with number()
	- convention, implementation details start with underscore. (Those are not for consumption & manipulation.)
	- do not use underscore variables directly

- class invariants
	- truths about an objeect endure for its its lifetime.

- law of Demeter
	- OOD principle.
	- only talk to your friend

- Base class can be abstract
	- can have functions that it's not in the base class.
		- of course it will error out when it's run in the base class, but works correctly in the derived class

-----

### file

open()

- open a file. 
	- file: path to file
	- mode: read/write/append, binary/text
		- text mode have a layer for universal newline characters
		- binary encoding is raw.
		- if encoding not specified, get default encoding
			- sys.getdefaultencoding
		- 'r' : open for reading (default)
		- 'w' : open for writing, truncating the file first
		- 'x' : open for exclusive creation, failing if the file already exists
		- 'a' : open for writing, appending to the end of the file if it exists
		- 'b' : binary mode
		- 't' : text mode
		- '+' : open a disk for updating
		- 'U' : universal newlines mode 
	- encoding : text encoding

~~~ python
>>> f = open('wasteland.txt', mode='wt', encoding='utf-8')
>>> f.write("King Kendrick! ")
>>> f.write("I have to supply newline char. \n"
>>> f.close()
~~~

~~~ python
>>> g = open('wasteland.txt', mode='rt', encode='utf-8')
>>> g.read(32)
# read till 32nd char
>>> g.read()
# read till end
>>> g.seek(0)
# move the pointer to 0th
>>> g.readline()
# line. if reached to end, empty string
>>> g.readlines()
['first sentence', 'second sentence']
~~~

- appending 

~~~ python
>>> h = open('wasteland.txt', mode='at', encoding='utf-8')
>>> h.writelines(
...		['Son of man, \n',
...		 'You cannot say, or guess, ' ,
...		 'for you know only,\n',
...		 'A heap of broken images, ',
...		 'where the sun beats\n'])
>>> h.close()
~~~

- files as iterator

~~~ python
# files.py
import sys


def main(filename):
	f = open(filename, mode='rt', encoding='utf-8')
	for line =in f:
		sys.stdout.write(line)
	f.close

if '__name__' = '__main__':
	main(sys.argv[1])
~~~

- with-block
	- resource cleanup with context managers
	- open() return context manager
	- do not have to write explicit f.close()

~~~ python
	with open(...)
		return [ int(line.strip()) for line in f]
~~~ 

- binary files
	- device independent bitmaps

----

Shipping Working and Maintainable Code

unittest

- unit tests
- integration test
- acceptance test
- TestCase
	- group together related test functions
	- basic unit of test organization in unittest
- fixtures
	- code run before and/or after each test function
- assertions
	- specific tests for condition and behaviors
		- x.isvalid()
		- x ==y
		- raise ValueError()
	- if asseriton fails, then a test fails

Requirement tests

- take file name
- read file
- calculate lines and character..

~~~ python
import unittest
import os

def analyze_text(filename):
	with oen(self.filename, 'r') as f:
		return sum(1 for _ in f)


class TextAnalysisTests(unittest.TestCase):
	"""Tests for the ''analyze_text()'' function."""
	
	def setUp(self):
		"""fixture that creates the file"""
		self.filename = 'text_analysis_test_file.txt"
		with open(self.filename, 'w') as f:
			f.write("hahaha\n"
					 "lolol")
	
	def tearDown(self):
		"""fixture that deletes the file"""
		try:
			os.remove(self.filename)
		except:
			pass
	
	def test_function_runs(self):
		"""basic smoke test: does the function run."""
		analyze_text(self.filename)
		
	def test_line_count(self):
		self.assertEqual(analyze_text(self.filename)[0], 2)
	
	def test_character_count
		self.assertEqual(analyze_text(self.filename)[1], somenumber)
	
	def test_no_such_file(self):
		with self.assertRaises(IOError): 
			analyze_text('foobar') 
	
if __name__ == '__main__':
	unittest.main()

~~~

PDB

- the python debugger
- in the module
	- set_trace will enter into the debugger

~~~ python
>>> import pdb
>>> pdb.set_trace()
(Pdb) help
...
...
...
~~~

Debugging a function

- executing pdb as a script

~~~ python
$ python3 -m pdb palindrome.py
--> import unittest
(Pdb) where
....../palindrome.py(1)
(Pdb) next
~~~

#### Packaging
I
- write setup.py

~~~ python 
from distutils.core import setup

setup(
	name='palindrome',
	version ='1.0',
	py_modules=['palindrome'],

	# metadata
	author='...'
	email='...'
)
~~~

- Setting up and importing

~~~
(venv) $ python setup.py install
>>> import palindrome
~~~

- will create a source distribution file

~~~
(venv) $ python setup.py sdist --format zip
~~~
 