#Java 8, From Collections to Streams Using Lambda Expressions

About...

- Lambda Expressions
- Building new functions and API
- The new Collection Framework
- Map / Filter / Reduce & collections
- The new Stream API

Will learn...

- To write lambda expressions in Java 8
- To leverage them to create new API
- To efficiently use the new collections
- To avoid useless computations in implementing map / filter / reduce
- To build and use simple streams

## Lambda

Basic comparator using a anonymous class
~~~ java
Comparator<String> comparator = new Comparator<String>() {
  public int compare(String s1, String s2) {
    return Integer.compare(s1.length(), s2.length());
  }
}
Arrays.sort(someStrings, comparator);
~~~

A runnable using a anonymous class
~~~ java
Runnable r = new Runnable() {
  public void run() {
    int i = 0;
    while (i++ < 10) {
      System.out.println("It Works!");
    }
  }
}
Executors.newSingleThreadExecutor.execute();
~~~

An anonymous class passed it to another piece of code.

Rewriting using Lambda
~~~ java
//comparator
Comparator<String> comparator =
  (String s1, String s2) ->
    Integer.compare(s1.length(), s2.length());
~~~

~~~ java
//runnable
Runnable r = () -> {
  int i = 0;
  while(i++ < 10) {
    System.out.println("It Works");
  }
}
~~~

In case needing a return value.

~~~ java
(String s1, String s2) -> {
  System.out.println("comparing strings");
  return Integer.compare(s1.length(), s2.length());
}
~~~

We can put modifier on the parameters of a lambda expression.
- final keywords
- annotations

But not possible to specify the returned type of a lambda expressions. But
we are not allowed to omit the data types of the parameters.

## Method References

Another way to write a lambda

First...
~~~ java
Function<Person, Integer> f = person -> person.getAge();
Function<Person, Integer> f = Person::getAge;
// Those two methods are exactly the same.
~~~

Second...
~~~ java
BinaryOperator<Integer> sum = (i1, i2) -> i1 + i2;
                            = (i1, i2) -> Integer.sum(i1, i2);

BinaryOperator<Integer> sum = Integer::sum;
BinaryOperator<Integer> max = Integer::max;
~~~

Third...
~~~ java
Consumer<String> printer = s -> System.out.println(s);
Consumer<String> printer = System.out::println;
~~~

## Creating new API

Interface have been modified in Java8

Default methods:
~~~ java
public interface Iterable<T> {
  default void forEach(Consumer<? Super T> action) {
    Objects.requireNonNull(action);
    for(T t : this) {
      action.accept(t);
    }
  }
}
~~~

Static methods:
~~~ java
@FunctionalInterface
public interface Function<T, R > {
  R apply(T t);

  static <T> Function<T, T> identity() {
    return t -> t;
  }
}
~~~

## Example w/ Comparator using default and static methods.

~~~ java
@FunctionalInterface
public interface Comparator<T> {
  public int compare(T t1, T t2) ;

  //original
  // public static Comparator<Person> comparing(Function<Person, Integer> f) {
  //   return (p1, p2) -> f.apply(p1) - f.apply(p2);
  // }

  //below function does not compile, because it is identifcal to the function above.
  //param is the same to the above function!
  // public static Comparator<Person> comparing(Function<Person, String> f) {
  //   return (p1, p2) -> -1
  // }

  //Change it to this
  // public static Comparator<Person> comparing(Function<Person, Comparable> f) {
  //   return (p1, p2) -> f.apply(p1).compareTo(f2.apply(p2));
  // }

  public default Comparator<T> thenComparing(Comparator<T> cmp) {
    return (p1, p2) -> compare(p1, p2) == 0 ? cmp.compare(p1, p2) : cmpare(p1, p2);
  }

  //One step further
  public static Comparator<U> comparing(Function<U, Comparable> f) {
    return (p1, p2) -> f.apply(p1).compareTo(f2.apply(p2));
  }
}

public class MainComparator {
  public static void main(String args[]) {
    Comparator<Person> cmpAge = (p1, p2) -> p2.getAge() - p1.Age();  
    Comparator<Person> cmpFirst = (p1, p2) -> p1.getFirstName().compareTo(p2.getFirstName());
    Comparator<Person> cmpLastName = (p1, p2) -> p1.getLastName().compareTo(p2.getLastName());  

    Function<Person, Integer> f1 = p -> p.getAge();
    Function<Person, String> f2 = p ->p.getLastName();
    Function<Person, String> f3 = p ->p.getFirstName();

    // Comparator<Person> cmpPerson = Compartor.comparing(f1);
    //or
    Comparator<Person> cmpPerson = Compartor.comparing(p -> p.getAge());
    //or
    Comparator<Person> cmpPersonAge = Compartor.comparing(p -> Person::getAge);
    Comparator<Person> cmpPersonLastName = Comparator.comparing(Person::getLastName);

    Comparator<Person> cmp = cmpPersonAge.thenComparing(cmpPersonLastName);
  }
}
~~~
