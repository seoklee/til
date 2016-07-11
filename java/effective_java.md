# Creating and Destroying Objects

##  Consider static factory methods instead of constructors

### Advantages Over a Public Constructor

- Static factory method can have names while a constructor cannot.
  - BigInteger(int, int. Random) -> BigInteger.probablePrime()
  - conveys a meaningful message to API users.
- Are not required to create a new object each time they are invoked.
  - Allows immutable classes to use preconstructed instances.
  - or allows to cache the instance when created..
  - similar to Flyweight pattern
  - gurantees singleton
  - nonintantiable
  - immutable class
- Can return subclasses.
- Can return objects without making their classes public.
- Hides the implement details
  - EnumSet backed by a single long or by a long array.
- Reduces verbosity of creating parameterized type instances
  ~~~ java
  //original
  Map<String, List<String>> m =
    new HashMap<String, List<String>>();

  //new
  public static <K, V> HashMap<K, V> newInstance() {
    return new HashMap<K, V>();
  }

  Map<String, List<String>> m = HashMap.newInstance();
  ~~~

### Disadvantages
- Classes without public or protected constructors cannot be subclassed.
- not readily distinguishable from other static methods

## Consider a Builder when faced with many constructor parameters
- If you have to build constructors... you are susceptible for a subtle bug
- JavaBean pattern is okay, but bit wordy.
~~~ java
NutritionFacts. cocalCola = new NutritionFacts();
cocaCola.setCalorie(100);
...
~~~
  - may be in an inconsistent state partway through its construction since the
  construction call is split across multiple calls.
  - The JavaBeans pattern precludes the possibility of making a class immutable.
- Use builder
  - setter returning the builder typed object so it can be chained (returning
  itself)
  - can make it immutable if makes the default into the final version
  - build can check invariant version of the object and throw exception.

  ~~~ java
  public NutiritionFacts build() {
    return new NutiritionFacts(this);
  }

  NutiritionFacts cocaCola = new NutiritionFacts.Builder(240, 8).calories(180).
  sodium(35).build();
  ~~~
- Even though it is negligible most of the time, building a builder can be an
overhead.

## Enforce the singleton property with a private constructor or an enum type

Singleton : a class that is instantiated exactly once.

~~~ java
//Singleton with public final field
public class Elvis {
  public static final Elvis INSTANCE = new Elvis();
  private Elvis() { ... }
  public void leaveTheBuilding() { ... }
}
~~~

~~~ java
//Singleton with static factory
public class Elvis {
  public static final Elvis INSTANCE = new Elvis();
  private Elvis() { ... }
  public static Elvis getInstance() { return INSTANCE; }
  public void leaveTheBuilding() { ... }
}
~~~

- but all in all, use a single-element enum type to implement singleton.

~~~ java
public enum Elvis{
  INSTANCE;

  public void leaveTheBuilding()
}
~~~

### Enforce noninstantiability with a private constructor.

- class of a grouping of static methods and static fields.
- attempting to enforce non-intantiability by making a class abstract does not work
  - the class can be subclassed and the subclass can be instantiated.
- the way is making a private constructor.

~~~ java
private UtilityClass() {
  throw new AssertionError();
}
~~~
- this also makes the class being subclassed.

## Avoid creating unnecessary objects

- Reuse objects if it's immutable.

~~~ java
String s = new String("stringette"); // DONT DO THIS
//"stringette" itself is a string instance. Creating duplicate, useless object.
String s = "stringette";
~~~

- Also can be abide by this rule if you use static factory method.
  - Boolean.valueOf(String) is better than Boolean(String)
- you can reuse mutable object if you know it will not get modified.

~~~ java
// Date objects are never modified after values are computed. BAD
private class Person {
  public boolean isBabyBoomer() {
    Calender gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
    gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
    Date boomStart = gmtCal.getTime();
    gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
    Date boomEnd = gmtCal.getTime();
    return birthDate.CompareTo(boomStart) >= 0 &&
        birthDate.compareTo(boomEnd) < 0;
  }
}
~~~

~~~ java
public class Person {
  private static final Date BOOM_START;
  private static final Date BOOM_END;

  static {
    Calendar gmtCal =
      Calendar.getInstance(TimeZone.getTimeZone("GMT"));
    gmtCal.Set(1946, Calendar.JANUAY, 1, 0, 0, 0);
    BOOM_START = gmtCal.getTime();
    gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
    BOOM_END = gmtCal.getTime();
  }

  public boolean isBabyBoomer() {
    return birthDate.compareTo(BOOM_START) >= 0 &&
           birthDate.compareTo(BOOM_END) < 0;
  }
}
~~~
- in this case, prefer primitive over boxed primitives.
- you can avoid object creation if the object is extremely heavyweight (i.e.
db connection)

## Eliminate obselete object references.

- nulling out reference in the stack (example)
  - nulling out object references should be the exception rather than th norm
- when a class manages its own memory, the programmer should be alert for memory leaks
- another common source of memory leaks is caches.
  - weakHashMap?
- A Third common source of memory leaks is listeners and other callbacks
WEAK REFERNCE

## Avoid Finalize

- finalizers are unpredictable, often dangerous, and generally unnecessary.
  - very few rules.
  - no guarantees of being called promptly.
    - never do time critical in finalize
    - depends on JVM
  - providing a finalizer for a class can, under rare conditions, arbitrarily delay reclamation of its instances.
    - finalize thread had low priority and 1000s of finalize were in queue. (anecdoctal stroy)
  - never depend on a finalizer to update critical persistent state.
  - even System.gc and System.runFinalization cannot guarantee it.
    - well. System.runFinalizersOnExit and Runtime.runFinalizersOnExit CAN do it. but dont use it. its depreceated.
  - severe perfoamcne penalty for using finalizer. (430 times slower in author's machine)
- provide an explicit termination method instead. (ex. close())
  - explicit termination methods are typically used in comivination with the try-finally construct to ensure termination.
  
  ~~~ java
  Foo foo - new Foo(...);
  try {
    // do stuff
  } finally {
    foo.terminate();
  }
  ~~~

- when is finalize useful?
  - act as a safety net in case the owner of an object forgets to call its explicit termination methods
    - even though there is no guarnetee that finalize will call. its better that the resource will maybe claimed back sometiems.
    - finalizer should log a warningif it finds that the resource has not been terminated.
  - Objects with native peers.
    - native peer is a native object to which a normal object delegates via native methods. (?)
      - with assumption that it carries no critical resource.
  - finalize is not chained.
    - if overriden finalize is called, you have to call the parent's finalize manually.
    - use try...finaly block!
    - creating a new anonymous object that finalizes is a good idea. (pg. 31)

# Methods Common to All Objects

## Obey the general contract when overriding equals

Overriding equals can bring really bad, critical errors. If you can, you shouldnt override it. You can pass by without overriding it if...

- Each instance of the class is inherently unique.
  - thread
- You dont care whether the class provides a "logical equality" test.
- superclass has already overriden equals, and the superclass' behavior is appropriate.
- the class is private or package-private, and you are certain that its equals method will never be invoked.

~~~ java 
//if you want to be safe
@Override
public boolean equals(Object o) {
  throw new AssertionError();
}
~~ 

When to use it?
- when logical equality matters. (value objects such as Date and Integer)

Sometimes enforcing both transitivity and symmetry is hard. One work around is to do composition; having a Point and Color variable in ColorPoint. (ex.)

** When creating a class. Think hard that it should be mutable or immutable. Do not write an equals method that depends on unreliable resources.**

Recipes for high-quality equals method

- use the == operator to check if the argument is a reference to this object.
- use the instanceof operator to check if the argument has the correct type
- cast the argument to the correct type
- for each 'significant' field in the class check if that field of the argument matches the corresponding field of this object
  - for float class, use Float.compare method and same thing goes for double
  - For performance reason, check the fields that are likely differ first.
- ALWAYS CHECK transitivity, symmetry, and consistency.
- Always override hashCode when you override equals
- Don't substitute another type for Object in the equals declaration!!!!

## Always override hashCode when you override equals

Malfunction in collections such as HashMap, HashSet, and Hashtable

- needs to return same integer when called.
- if two objects are equal in .equal method, they need to have same hashcode.
- producing distinct integer results for unequal objects may improve the performance of hash tables.

Good HashCode produces uneqaual hash code for different objects

1. store some constant nonzero value at an int variable.
2. for each significant field in your object...
  - if the field is a boolean, compute (f ? 1 : 0).
  - if the field is a byte, char, short, or int, compute (int) f.
  - if the field is a long, compute (int) (f ^ (f >>> 32))
  - If the field is a float, compute Float.floatToINtBits(f).
  if the field is a double, compute Double.doubleToLongBits(f), and then hash the resulting long as in step 2.a.iii.
  - if the field is an object reference and this class's equals method compares the field by recursively invoking equals, recursively invoke hashCode on the field. For a complex comparison, use canonical representation, and invoke hashCode on the canonical representation. for a null value, return 0.
  - If the field is an array, treat it as if each element were a separate field. Apply hashCode to every element and comvine these values per with 2.b
  b. combine the has code c computed in step2.a into result as follows:
  ~~~ java
  result = 31 * result + c;
  ~~~

You should exclude fields that are not used in .equals method.

okay, on-par, acceptable hasCode 

~~~ java
@Override
public int hashCode() {
  int result = 17;
  result = 31 * result + areaCode;
  result = 31 * result + prefix;
  result = 31 * result + lineNumber;
  return result;
}
~~~

If a class is immutable and the cost of computing hash code is significant... consider caching the hash code in the object rather than recalculating it.

~~~ java
// lazily initialized. cached.
private volitaile int hashCode;

@Override
public int hashCode() {
  int result = hashCode;
  if (result = 0) {
    int result = 17;
    result = 31 * result + areaCode;
    result = 31 * result + prefix;
    result = 31 * result + lineNumber;
    hashCode = result;
  }
  return result;
}
~~~

** Do not be tempted to exclude significant parts of an object from the hash code computation to improve performance fam **

## Always override toString

just do it fam. good practice. makes debugging easy, and make it more pleasant overall.

When practical, the toString method should return all of the interesting information contained in the object.

one important decision is to whether to specify the format of the return value in the documentation. You should do it if your class is a value classes.  if you want to do it this way, you ought to have a matching constructor or static factory method to do so.

if you specify the format. That will be should be the final format. If your program is widely used, and if you change format at different releases, your users will be angry. :/

## Override clone judiciously.

Cloneable returns a field by field copy of the object if a class implements clonable or throws CloneNotSupportedException. That normal behaviour is probably not what one wants.

---
Few contracts from java documentation is...

1.
~~~ java
x.clone() != x
~~~

2.
~~~ java
x.clone().getClass() != x.getClass()
~~~

3.
~~~ java
//this is not an absolute requirement
x.clone().equals(x)
~~~

4.  No Constructors are called.

---

No constructors are called is too strong. 
A well-behaved clone method can call constructors to create 
objects internal to the clone under construction.

If you override the clone method in a nonfinal class, you should
return an object obtained by invoking super.clone. If this is all implemented
the right way, it will bubble up to the Object's clone method.

In practice, a class that implements Cloneable is expected to provide a properly
functioning public clone method.

In effect, the clone method functions as another constructor; you must ensure
that it does no harm to the original object and that it properly establishes
invariants on the clone.

The clone architecture is incompatible with normal use of final fields referring
to mutable objects.

you are better off providing an alternative means of object copying, or simply not
providing the capability

A fine approach to object copying is to provide a copy constructor or copy factory

---

## Consider implementing comparable

Just makes stuff so much easier. You can then use methods such as Arrays.sort() and
maybe just put stuff on TreeSet.

CompareTo similar to equals and have a same caveat with equals. It become hard
to compare two similar differently typed object.

Pro-tip? Write another unrelated class containing an instance of the first class
and provide a "view" method that returns this instance (same advice as in the
equals method)

Sorted collections use compareTo instead of .equals()

no need to type check. if you give it a wrong type, it will not compile.

compare integral primitive fields using the relational operator < and >. For
floating-point fields, use Double.compare or Float.compare in place of the relational
operator.

if class has multiple significant fields, order them as how critical they are.

you can use subtraction and check sign only for cleaner code. This only works for
smaller value. If this goes over 2^31, don't use it.
