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

