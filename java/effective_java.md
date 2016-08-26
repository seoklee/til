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

  public void leaveTheBuilding() {}
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

#### you are better off providing an alternative means of object copying, or simply not providing the capability

#### A fine approach to object copying is to provide a copy constructor or copy factory

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

---

## Minimize the accessibility of classes and members

Well designed module hides all of its implementation details, cleanly separating its API from its implementation. (Encapsulation fam)

##### make each class or member as inaccessible as possible.

It's the interface itself that can be package-private, not the methods in it.

If a package-private top-level class (or interface) is used by only one class, consider making the top-level class a private nested class of the sole class that uses it. 

Instance fields should never be public. If an instance field is non-final, or is a final reference to a mutable objet, then by making the final public, you give up the ability to limit the values that can be stored in the field. 

Classes with public mutable fields are not thread-safe even if a field is final and refers to an immutable object. By making the field public you give up the flexibility to switch to a new internal data representation in which the field does not exist.

This applies to static variable, except static constants. Constants are allowed as long as it is a integral part of the abstraction provided by the class. (Capital letters and underscores in the names). These variables only contain primitive values or immutable objects. A final field containing a reference to a mutable object has all the disadvantages of a nonfinal field. (while the reference cannot be modified, the referenced object can be modified.)

It is wrong for a class to have a public static final array field, or an accessor that returns such a field.

~~~ java
//potential security hole?
public static final Thing[] VALUES = { ... } ;

//to fix...
private static final Thing[] PRIVATE_VALUES = { ... };
public static final List<Thing> Values = 
  Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));

//or
public static final Thing[] values() {
  return PRIVATE_VALUES.clone();
}
~~~

----

## In public classes, use accessor methods, not public fields.

However if a class is package-private or is a private nested class, there is nothing inherently wrong iwth exposing its data fields.

Even though it's never a good idea to expose public fields, if it does, it is less harmful when it's immutable.

## Minimize Mutablility

immutable objects = objects whose instances cannot be modified. All of the info contained in each instance is provided when it is created and is fixed for the lifetime of the object.

Immutable objects are easier to design, implement, and use than mutable classes. They are inherently thread-safe, too.

Follow these rules to make your object immutable

1. Don't provide any methods that modify the object's state
2. Ensure taht the class can't be extended. 
  - Generally accomplished by making the class final.
3. Make all fields final
4. Make all fields private.
  - While it is technically permissible for immutable classes to have public final fields containing primitive values or references to immtuable objects, it is not recommended becasue it precludes changing the internal representation in a later release.
5. Ensure exclusive access to any mutable components.
  - If your class has any fields that refer to mutable objects, ensure that clients of the class cannot obtain references to these objects. Never initialize such a field to a client-provided object reference or return the object reference from an accessor. Make defensive copies in constuuctors, accessors, and readObject methods.

Static factories can be used to keep a cache of frequently used item if it is immutable. Reduces memory ootprint and GC costs.

You never have to make defensive copies. No clone or copy constructor.

You can also share their internals. (BigInteger with sign and magnitutde example)

Immutable objects make great building blocks for other objects.

The only real disadvantage is that immutable objects require a separate object for each distinct value. Could be a performacne issue. Also if an operation produces one object in final but produces numerous intermediate objects.. The solution is to use primitive at those multistep operation.

if you cannot accuragtely predict which complex multistage operations clents will wnat to perform on your immutable class, provide a public mutable companion class. (Stringbuilder and String)

If you want to force in-extendibility, you can use final keyword or make all of its contructors private or package-private and to add public static factories. It is flexible because it allows the use of multiple package-private implementation classes. This also adds more flexibility in terms of creating an object with different values because you can simply add another static factory method (i.e. complex number with polar coordinate).

The rule, immutable classes should not have a methods that modify itself and all of its fields must be final, can be relaxed for a performance issue. In truth, no method may produce an externally visible change in the object's state. However, some immutable classes have one or more nonfinal fields in which thethey cache the results of expensive computations the first time they are needed.

If immutable class implement Serializable and it contains one or more fields that refer to mutable objects, you must provide an explicit readObject or readResolve method. (or use the OjbectOutputStream.writeUnshared and ObjectInputStream.readUnshared methods, even if the default serialized form is acceptable.)

##### Classes should be immutable unless there's a very good reason to make them mutable. If a class cannot be made immutable, limit its mutability as much as possible. Make every field final unless there is a compelling reason to make it nonfinal.

## Favor composition over inheritance

Inheritnace is safe to use within a package or when extending classes specifically designed and documented for extension. Extending across the package boundary can be dangerous

Unlike method invocation, inheritance violates encapsulation. If you extend across the package and superclass functionality changes, your code breaks.

Solution? give your new class a private field that references an instance of the existing class. (composition because the existing class becomes a component of the new one). Each instance method in the new class invokes the corresponding method on the contained instance of the existing class and returns the result. (This is called forwarding, and the methods in the new class are known as forwarding methods.)

~~~ java
//wrapper class - uses composition rather than inheritance
public class InstrumentedSet<E> extends ForwardingSet<E> {
  private int addCount = 0;

  public InstrumentedSet(Set<E> s) {
    super(s);
  }

  @Override public booldean add(E e) {
    addCount++;
    return super.add(e);
  }
  ...
}

//Reusable forwarding class
public class ForwardingSet<E> Implements Set<E> {
  private final Set<E> s;
  public FowardingSet<E>(Set<E> s) { this.s = s; }

  public void clear() {
    s.clear();
  }

  public boolean add(E e) {
    return s.add(e)
  }
  ...
}
~~~

This is more flexible; any Set implementation and will work in conjunction with any preexisting constructor;

~~~ java
Set<Date> s = new InstrumentedSet<Date>(new TreeSet<Date>(cmp));
Set<E> s2 = new InstrumentedSet<E>(new HashSet<E>(capacity));
~~~

InstrumentedSet class is known as a wrapper class because each InstrumentedSet instance contains another Set isntnace. This is also known as decorator pattern

This is not suitable for a callback frameworks wherein objects pass self-references to other objects for subsequent invocations. Because a wrapped object does not know of its wrapper, it passes areference to self and callbacks elude the wrapper.

Memory footprint impact and performance impact is minimal. It's tedious to write forwarding methods, but you have to write the forwarding class for each interface only once, and forwarding classes may be provided for you by the package containing the interface.

extend if and only if the relationship is 'is-a'

if you use inheritnace where composition is appropriate, you needlessly expose implementation details, tying you to the original implementation. The client may be able to corrupt invariants of the subclass by modifying the superclass directly.

Also think whether your superclass has flaws, if it does, it propagates the flaw to the subclasses using inheritance.

## Design and document for inheritance or else prohibits it

the class must document precisely the effects of overriding any method. Meaning, class must document its self-use of overridable methods. (any circumstances under which it might invoke an oberridable method.)

 allow programmers to write efficient subclasses without undue pain, a class may have to provide hooks into its internal workings in the form of judiciously chosen protected methods or, in rare instances, protected fields.

The only way to test a class designed for inheritance is to write subclasses. If you omit a crucial protected member, trying to write a subclass will make the ommision painfully obvious. Conversely if several subclasses are written and non uses a protected member, you should probably make it private.

Few more restrictions that a class must obey to allow inheritance.

Constructors must not invoke overridable methods, directly or indirectly. The superclass constructor runs before the subclass constructor, so the overriding method in the subclass will get invoked before the subclass constructor has run. If the overriding method depends on any initaalization performed by the subclass constructor, the method will not behave as expected.

neither clone nor readObject may invoke an overridable method, directly or indirectly (pg. 90)