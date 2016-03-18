#Design Pattern

##Creational Design Pattern

- How objects are built
	- Singleton
	- Builder
	- Prototype
	- Factory
	- AbstractFactory

---

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


Ex.

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

##Builter Pattern

- Handles a complex constructor
	- large amount of parameters
- immutability
- example
	- StringBuilder
	- DocumentBuilder
	- Locale.Builder

- Filexibility over telescoping constructors
	- telescoping constrcutors : creating multiple constructors by altering each parameter. 
- static inner class
- calls appropriate constructor
- negates the need for exposed setter
- can take advantage of generics

Example : Stringbuilder

~~~ java
StringBuilder builder = new Stringbuilder();
builder.append("This is an example ");
builder.append("of the builder pattern ");
builder.append("42");
//build.build()
System.out.println(build.toString());
~~~

###Demonstrate Exposed Setter

~~~ java
// mutable. does not signify which is required, etc

public class LunchOrderBean {
	private String bread;
	private String condiment;
	private String dressing;
	private String meat;
	
	public LunchOrderBean() {
		
	}
	
	//getters and setters
}
~~~

###Demonstrate Telescoping Consturctors

~~~ java
// Immutable... but what if I want a lunch with meat and lettuce only? 

public class LunchOrderTele {
	private String bread;
	private String condiment;
	private String dressing;
	private String meat;
	
	public LunchOrderTele(String bread) {
		this.bread = bread;
	}

	public LunchOrderTele(String bread, String codiments) {
		this(bread);
		this.codiments = codiments;
	}

	public LunchOrderTele(String bread, String codiments, String dressing) {
		this(bread, codiments)
		this.dressing = dressing;
	}

	public LunchOrderTele(String bread, String codiments, String dressing, String meat) {
		this(bread, codiments, dressing)
		this.meat = meat;
	}	
	
	//getters
}
~~~

###Create Builder

~~~java
//immutable and flexible
public class LunchOrder {

	public static class Builder {
		private String bread;
		private String condiment;
		private String dressing;
		private String meat;
		
		public LunchOrder build() {
			return new LunchOrder(this);
		}
		
		public Builder bread(String breadk) {
			this.bread = breadk;
			return this;
		}
		
		public Builder condiments(String condiments) {
			this.condiments = condiments;
			return this;		
		}
		
		public Builder meat(String meat) {
			this.meat = meat;
			return this;		
		}
		
		public Builder dressing(String dressing) {
			this.dressing = dressing;
			return this;		
		}		
		
	}
	
	private final String bread;
	private final String condiment;
	private final String dressing;
	private final String meat;
	
	private LunchOrder(Builder builder) {
		this.bread = builder.breadk;
		this.codiments = builder.condiments;
		this.dressing = builder.dressing;
		this.meat = builder.meat
	}
	
	//getters
}

~~~
~~~ java
public class BuilderLunchOrderDemo(String args[]) {
	LunchOrder.Builder builder = new LunchOrder.Builder();
	builder.bread("Wheat").condiments("Lettuce").dressing("Mayo").meat("Turkey");
	
	LunchOrder lunchOrder = builder.build();

}
~~~

###Pitfalls

- immutable
- inner static class
- designed first
- complexity

###Contrast

Builder

- handles complex constructors
- no interface required
- can be a separate class
- works with legacy code

Prototype

- implemented around a clone
- avoids calling complex constructor
- difficult to implement in legacy code

---

# Prototype

- avoids costly creation
- avoids subclassing
- typically does not use "new"
- often utilizes an interface
- Usually implemented with a Registry
- Example
	- java.lang.Object#clone()

- costly to create... but copying the member variable will be good? use this
- clone/cloneable
- avoids keyword "new"
- although a copy, each instance unique
- costly constructor not handled by client
- can still utilize parameters for constructor
- shallow vs deep copy

~~~ java
publc class Statement implements Cloneable {
	
	public Statement(String sql, List<String> parameters, Record record) {
		this.sql = sql;
		this.parameters = parameters;
		this.record = record;
	}
	
	public Statement clone() {
		try {
			return (Statement) super.clone();
		} catch (CloneNotSupportedException e) {
			e.printStackTrace();
		}
		return null;
	}
}


public class Record {

}
~~~

~~~ java
//shallow clone
public class PrototypeEverydayDemo {
	
	public static void main(String args[]) {
		String sql = "select * from movies where title = ?";
		List<String> parameters = new ArrayList<>();
		parameters.add("Star wars");
		Record record = new record();
		Statement firstStatement = new Statement(sql, parameters, record);
		Statement secondStatement = firstStatement.clone()
		//second statement's and first statment's record are the same reference.
	}
}


~~~ 

~~~ java
public abstract class Item implements Clonable {
	private String title;
	private double price;
	private String url;
	
	@Override
	protected Object clone() throws CloneNotSupportedException {
		return super.clone();
	}
	
	//getter and setters.
}

public class Books extends Item {
	//getter and setter
}

public class Movie extends Item {
	//getter and setter
}

public class Registry {
	private map<String, Item> items = new HashMap<String, item>();
	public Registry() {
		loadItems();
	}
	
	public Item createItem(String type) {
		Item item = null;
		
		try {
			item = (Item)items.get(type)).clone();
		} 
		catch (CloneNotSupportedException e) {
			e.printStackTrace();
		}
		
		return item
	}
	
	private void loadItems() {
		Movie movie = new Movie();
		movie.setTitle("Basic Movie");
		..
		items.put("Movie", movie);
		
		Book book = new Book();
		book.setNumberOfPages(232);
		...
		items.put("Book", book);
	}

}
~~~

~~~ java

public static void main(String[] args) {
	Registry registry = new registry();
	Movie movie = (Movie) registry.createItem("Movie");
	movie.setTitle("Creational Patterns in Java");
	
	System.out.println(movie);
	System.out.println(movie.getRuntime());
	System.out.println(movie.getTitle());
	System.out.println(movie.getUrl());
	
	Movie anotherMovie = (Movie)registry.createItem("Movie");
	anotherMovie.setTitle("Gang of Four");
	
	System.out.println(anotherMovie);
	System.out.println(anotherMovie.getRuntim());
	System.out.println(anotherMovie.getTitle());
	System.out.println(anotherMovie.getUrl());
}

~~~

### Pitfalls

- sometimes not clear when to use
- used with other patterns
	- Registry
- Shallow VS Deep Copy

####Prototype

- Lighter weight construction
	- copy constructor or clone
- shallow vs deep
- copy of itslef

####Factory

- Flexible objects
	- multiple constructor
- Concrete instance
- Fresh Instance

---

#Factory

- opposite of singleton
- does not expose instantiation logic
- defer to subclasses
- common interface
- specified by architecture, implemented by user
- example
	- Calendar
	- ResourceBundle
	- NumberFormat


### Design
- factory is responsible for lifecycle
- concrete classes
- parameterized create method

~~~ java
Calendar cal = calendar.getInstance();
System.out.println(cal);
System.out.println(cal.get(Calendar.DAY_OF_MONTH));
~~~

### Exmaple

Website generators

~~~ java
Page.java
publc abstract class Page {

}

CartPage.java
public class CartPage extends Page {

}

//various other Pages.java

Website.java
public abstract class Website {

	protected list<Page> pages = new ArrayList<>();
	
	public List<Page> getPages() {}
	
	public WebSite() {
		this.createWebsite();
	}
	
	public abstract void createWebsite();
}

Blog.java
public class Blog extends Website {

	@Override
	public void createWebsite() {
		pages.add(new PostPage());
		pages.add(new AboutPage());
		pages.add(new CommentPage());
		pages.add(new ContactPage());
	}
}

Shop.java
public class Shop extends Website {

	@Override
	public void createWebsite() {
		pages.add(new ShopPage());
		pages.add(new ItemPage());
		pages.add(new SearchPage());
	}
}


//Creating enum for the type of object is a good idea
public enum WebsiteType {
	BLOG,SHOP
}

WebsiteFactory.java
public class WebsiteFactory {

	public static Website getWebsite(WebsiteType siteType) {
		switch(siteType) {
			case BLOG : {
				return new Blog();
			}
			
			case SHOP : {
				return new Shop();
			}
			
			default: {
				return null;
			}
		}
	}
}
~~~

### Pitfall

- complexity
- creation in subclass
- refactoring

###Contrast

Singleton

- returns same instance
	- one constructor method - no args
- no interface
- no subclasses

Factory

- returns various instances
	- multiple constructors
- interface driven
- subclasses
- adaptable to environment more easily

---

#AbstractFactory

- factory of factories
- factory of related objects
- common interface
- defer to sublcasses
- example:
	- DocumentBuilder
	- Frameworks

## Design

- Groups factories together
- factory is responsible for lifecycle
- common interface
- parameterized created method
- ####composition

~~~ java
DocumentBuilderFactory abstractFactory = DocumentBuilderFactory.newInstance();
DocumentBuilder factory = abstractFactory.newDocumentBuilder()
Document doc = factory.parse(bais);
~~~

## Exercise

~~~ java
public abstract class CreditCardFactory {
	
	public static CreditCardFactory getCreditCardFactory(int creditScore) {
		if(creditScore > 650) {
			return new AmexFactory();
		}
		else {
			return new VisaFactory();
		}
	}
	
	public abstract CreditCard getCreditCard(CardType);
	
	public abstract Validator getValidator(CardType cardType);
}

//VisaFactory.java
public class VisaFactory extends CreditCardFactory {
	@Override
	public CreditCard getCreditCard(CardType cardType) {
		switch(cardType) {
			case GOLD:
				return new VisaGoldCreditCard();
			case PLATINUM:
				return new VisaPlatCreditCard();
				
			default:
				break;
		}
		
		return null;
	}	
	
	@Override
	public Validator getValidator(CardType cardType) {
		
		switch(cardType) {
			case GOLD:
				return new VisaGoldValidator();
			case PLATINUM:
				return new VisaPlatValidator();
		}
	
		return new VisaValidator();
	}	
}

~~~

## Pitfall

- Complexity
- Runtime switch
- Pattern within a patern
- Problem specific
- Starts as a Factory

## Contrast

###Factory

- returns various instances
	- multiple constructors
- interface driven
- adaptable to environment more easily

###AbstractFactory

- Implemented with a factory
- hides the factory
- abstracts environment
- built through composition.

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