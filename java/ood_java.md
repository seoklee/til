#Java OOD Concepts

I usually forget some of the details that I listed below. I just reviewed the key points that I should know.

##Abstract

- Abstract class cannot be instantiated. 

Someimtes, it looks like you are instantiating an abstract class. But no, you are actually instantiating an annoynous subclass.)
	
- Abstract method cannot have a body

This means, there is no implementation normally; a deriving class should implement some logic to use this method. 

- If a class owns an abstract method, it must be abstract class

But, the opposite is not necessarily true. An abstract class may or may not have abstract classes.
Also, an abstract class may also have non-abstract methods.

- If you inherit (extends) an abstract class, you have to implement all of the abtract methods.

##Interface

- You have to implement all of the methods defined in an interface if a class is implementing  it.

- A class can implement multiple interfaces.

- An interface can inherit(extend) another interface.

- Interface members are all public.

Kinda makes sense. The implementations need to access the methods in order to impelment them.

- Interface cannot be instantiated.

Though, an object that implements the interface can be referred with the interface type.

##Other

- A class implicitly inherit Object class.

- A class can have a 'final' method if that method should not be inherited. 

- A constructor of enum cannot be public.

- You cannot use primitive data type for generics.
