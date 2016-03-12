#Design Pattern

##Creational Design Pattern

- How objects are built
	- Singleton
	- Builder
	- Prototype
	- Factory
	- AbstractFactory

##Singleton

- Only one instance created
- guarantees control of a source
- lazily loaded
- Ex
	- Runtime
	- Logger
	- Spring Beans
	- Graphics Managers

- CLass is responsible for lifecycle
- static in nature
- needs to be thread safe
- private instance
- private constructor


~~~ java
Runtime singletonRuntime = Runtime.getRuntime();

singletonRuntime.gc();

System.out.println(singletonRuntime);


Runtime anotherInstance = Runtime.getRuntime();

System.out.println(anotherInstance);

if(singletonRuntime == anotherInstance) {
	System.out.println("They are the same instance");
}
~~~

Ex.

### Create Singleton
### Demonstrate only one instance created

~~~ java
DbSingleton.java

package com.plurasight.singleton

public class DbSingleton {
	private static DbSingleton instance = new DbSingleton();
	private DbSingleton() {
	
	}
	public static getInstance() {
		return instance;
	}
}
~~~

~~~ java
DbSingletonDemo.java 

package com.plurasight.singleton

public class DbSingletonDemo {
	public static void main(String arg[]) {
		DbSingleton instance = DbSingleton.getInstace();
		
		//ERROR
		DbSingleton anotherInstance = new DbSingleton();
	}

}
~~~

### lazy loaded

~~~ java
DbSingleton.java

package com.plurasight.singleton

public class DbSingleton {
	private static DbSingleton instance = null;
	
	private DbSingleton() {
	
	}
	public static getInstance() {
		if(instance == null) {
			instnace = new DbSingleton();
		}
		return instance;
	}
}
~~~ 

### thread safe operation

- you only want to run it thread-safe when the instance is null

~~~ java
DbSingleton.java

package com.plurasight.singleton

public class DbSingleton {
	private static DbSingleton instance = null;
	
	private DbSingleton() {
	
	}
	public static getInstance() {
		if(instance == null) {
			synchronized (DbSingleton.class) {
				if(instance == null) {
					instnace = new DbSingleton();
				}
			}
		}
		return instance;
	}
}
~~~

###Singleton

- returns same instance
	- one constructor method - no args
- No Interface

###Factory

- Returns various instances
	- multiple constructor
- Interface driven
- Adaptable to environment more easily

### Pitfall

- often used
- difficult to unit test
- if not careful, not thread-safe
- 

---

#Structural Design Pattern

Each design patterns differs by the patten organization and by the language

- Whether it be a funcitonal pattern, objected oriented pattern... etc

Design pattern matters

- gives our profession a shared language
- avoid re-inventing constantly
- provide a starting point for a solution
	- like a template
- can speed production in a team
- generally improves system and applicaiton design

##Adapter Design Pattern

- does not implement the interface you require
- designing a class or a framework and you want to ensure it is usable by a wide variety of as-yet-unwritten classes and applications
- Adapter aka Wrapper

Wrapper will 'wrap' what you have, so you can use that object with wrapper into the component that component expects.

Use it when..

- wnat to use an existing class, but its interface does not match the one you require
- want to create reusable class that cooperates with unrelated or unforseen classes.

- client depend on the adapter interfcae, rather than a particular implementation
- at least one concrete Adapter class is created to allow the client to work with a particular class that it requires
- A single adapter interface may work with many adaptees
	- one adaptee and all of its subclass
	- separate adaptees via separate concrete Adapter implementation
- can be difficult to override Adaptee behavior
	- must subclass adaptee and add overriden behavior
	- then change concrete Adapter implementation to refer to Adaptee subclass

Ex. Arrays -> Lists
	Streams
	
~~~ java
Integer[] arrayOfInts = new Integer[] { 42, 43, 44};
List<Integer> listOfInts = Arrays.asList(arrayOfInts);
System.out.println(arrayOfInts);
System.out.println(listOfInts);
~~~

another example

~~~ java
public class EmplyeeLdapAdapter implements Employee {
	//blahblah
}
~~~


~~~ java
List<Employee> le = new ArrayList<Employee>();
Employee em = new EmployeeFDB("lee", "seok", "seok.f.lee@gmail.com");
//works
le.add(em)

EmployeeLdap eldap = new EmployeeLdap("tyler", "massey", "dfjkl@df.com");
EmployeeCSV ecsv = new EmployeeCSV("tyler,massey,dfjkl@df.com");

//will not work

//le.add(eldap);
//le.add(ecsv);

//You should...
le.add(new EmployeeLdapAdapter(eldap));
le.add(new EmployeeAdapterCSV(ecsv));
~~~

Adapter

- works after code is designed
- legacy
	- can't and does not want to chnage
- retrofitted
- provides different interface

Bridge
- Designed upfront
- Abstraction and Implementation vary
- built in advance
- both adapt multiple systems