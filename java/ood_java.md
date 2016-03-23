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

---

## Enum

enum is essentially a class.

 - Private constructor cause it to not to be instantiated. 

~~~ java
enum Fruit{
    APPLE, PEACH, BANANA;
}

//This is equivalent to the enum Fruit

/*
class Fruit{
    public static final Fruit APPLE  = new Fruit();
    public static final Fruit PEACH  = new Fruit();
    public static final Fruit BANANA = new Fruit();
    private Fruit(){}
}
*/

enum Company{
    GOOGLE, APPLE, ORACLE;
}

 
public class ConstantDemo {
     
    public static void main(String[] args) {
        Fruit type = Fruit.APPLE;
        switch(type){
            case APPLE:
                System.out.println(57+" kcal");
                break;
            case PEACH:
                System.out.println(34+" kcal");
                break;
            case BANANA:
                System.out.println(93+" kcal");
                break;
        }
    }
}
~~~

enum itself can have a constructor. But it has to be private

~~~ java
enum Fruit {
	APPLE, PEACH, BANANA;
	Fruit() {
		System.out.println("Call Constructor " + this);
	}
}
~~~

If we changed the fruit enum to the above code, and run the code in the main. The result will look something like this

~~~ 
Call Constructor APPLE
Call Constructor PEACH
Call Constructor BANANA
57 kcal
~~~

You also can have attributes and method in the enum. 

~~~ java
enum Fruit{
    APPLE("red"), PEACH("pink"), BANANA("yellow");
    private String color;
    Fruit(String color){
        System.out.println("Call Constructor "+this);
        this.color = color;
    }
    String getColor(){
        return this.color;
    }
}
~~~

.value() lists all the enums

~~~ java
for(Fruit f : Fruit.values()){
    System.out.println(f+", "+f.getColor());
}
~~~

---

## Generic

~~~ java
exmaple

class Person<T>{
    public T info;
}
 
public class GenericDemo {
 
    public static void main(String[] args) {
        Person<String> p1 = new Person<String>();
        Person<StringBuilder> p2 = new Person<StringBuilder>();
    }
 
}
~~~


The below code makes sense; String does not have rank attribute.

~~~ java
another example

class StudentInfo{
    public int grade;
    StudentInfo(int grade){ this.grade = grade; }
}
class EmployeeInfo{
    public int rank;
    EmployeeInfo(int rank){ this.rank = rank; }
}
class Person<T>{
    public T info;
    Person(T info){ this.info = info; }
}
public class GenericDemo {
    public static void main(String[] args) {
        Person<EmployeeInfo> p1 = new Person<EmployeeInfo>(new EmployeeInfo(1));
        EmployeeInfo ei1 = p1.info;
        System.out.println(ei1.rank); // No Error
         
        Person<String> p2 = new Person<String>("Seinor");
        String ei2 = p2.info;
        System.out.println(ei2.rank); // Compiler Error
    }
}
~~~

Generic cannot use primitive data type. So the wrapper data type such as Integer has to be used. 

~~~ java
class EmployeeInfo{
    public int rank;
    EmployeeInfo(int rank){ this.rank = rank; }
}
class Person<T, S>{
    public T info;
    public S id;
    Person(T info, S id){ 
        this.info = info;
        this.id = id;
    }
}
public class GenericDemo {
    public static void main(String[] args) {
        EmployeeInfo e = new EmployeeInfo(1);
        Integer i = new Integer(10);
        Person<EmployeeInfo, Integer> p1 = new Person<EmployeeInfo, Integer>(e, i);
        System.out.println(p1.id.intValue());
    }
}
~~~

You can also use generic on method. Look at printInfo. 


~~~ java
class EmployeeInfo{
    public int rank;
    EmployeeInfo(int rank){ this.rank = rank; }
}
class Person<T, S>{
    public T info;
    public S id;
    Person(T info, S id){ 
        this.info = info;
        this.id = id;
    }
    public <U> void printInfo(U info){
        System.out.println(info);
    }
}
public class GenericDemo {
    public static void main(String[] args) {
        EmployeeInfo e = new EmployeeInfo(1);
        Integer i = new Integer(10);
        Person<EmployeeInfo, Integer> p1 = new Person<EmployeeInfo, Integer>(e, i);
        p1.<EmployeeInfo>printInfo(e);
        p1.printInfo(e);
    }
}
~~~

You can limit a class in generic by specifying it. Person<T Extends Info> will only excepts only subclass that extends/implements Info. 

~~~ java
package org.opentutorials.javatutorials.generic;
abstract class Info{
    public abstract int getLevel();
}
class EmployeeInfo extends Info{
    public int rank;
    EmployeeInfo(int rank){ this.rank = rank; }
    public int getLevel(){
        return this.rank;
    }
}
class Person<T extends Info>{
    public T info;
    Person(T info){ this.info = info; }
}
public class GenericDemo {
    public static void main(String[] args) {
        Person p1 = new Person(new EmployeeInfo(1));
        Person<String> p2 = new Person<String>("부장");
    }
}
~~~