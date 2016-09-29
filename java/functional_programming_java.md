# Functional Programming w/ Java

## Basic of Functional Programming

- Functions as values
  - Store functions in variables
  - pass functions in parameters
  - return functions from other functions

~~~
public static void main(final String[] args) {
  IntToDoubleFunction incrementalCosts = time -> 5.1 + 0.15 * TIME;
  Function<String, Integer> wordCount = s -> s.split(" ").length;
  BiFunction<String, Integer, Boolean> exceedsMaxLength = (s, maxLength) -> {
    int actualLength = s.length();
    return actualLength > maxLength;
  }
}
~~~

Lost...

- type safety
- properties
- gain code duplication

Gain...
- no hierarchy
- simpler
- easy to add

~~~ java
@FunctionalInterface
public interface FunctionOverTime {
  double valueAt(int time);

  static FunctionOverTime monthByMonth(final double[] values) {
    return time-> values[time - 1];
  }

  static FunctionOverTime constant(final double value) {
    return polynomial(new double[] { value });
  }

  static FunctionOverTime line(final double intercept, final double slope) {
    return polynomial(new double[] { intercept, slope});
  }

  static FunctionOverTime polynomial(final double[] coefficients) {
    return time -> {
      Double sum = 0.0;
      for(int i = 0; i < coefficients.length; i++) {
        sum += Math.pow(time, i) * coefficients[i];
      }
      return sum;
    };
  }
}
~~~

Function is not attached to an instance of the class. But a method is. Function
only depends only on their input.

Function does not.. (aka data in, data out)
- access global state
- modify input
- change the world

@FunctionalInterface and static method are functions.

Closure is a bit different; closure can change the variable around it, lambda cannot.

Referential Transparency = (DIDO, data in & data out)

Treat functions as values.

-------------

Passing code as value remove code duplication.

Ex. Finding out how much each function takes to do its work.

~~~ java
//before
public class Example {
  public static void main(final String[] args) {
    final Date beforeCosts = new Date();
    final double costs = calculateCosts();
    final Long costMillis =
      new Date.getTime() - beforeCosts.getTime():
    System.out.println("Cost calculation took " + costMillis + "milliseconds ");

    //... repeat n times for n functions.
  }
}
~~~

~~~ java
public class Timing {
  public static Double timed(String description, Supplier<Double> code) {

    final Date before = newDate();
    Double result = code.get();
    final Long duration = new Date().getTime() - before.getTime();
    System.out.println(description + "took" + duration + " milliseconds");

    return result;
  }
}
~~~


~~~ java
//before
public class Example {
  public static void main(final String[] args) {
    final Double costs = timed("Cost calculation"), Example::calculateCosts);
    final Double revenue = timed("Revenue calculation", Example::calculateRevenue);
    final Double profit = timed("Profit calculation", () -> calculateProfit(costs, revenue));

    System.out.println("Profit = $" + profit);
  }
}
~~~

~~~ java
//make it better using generic
//also println makes assumption that you are using console somewhere...
public class Timing {
  public static <A> A timed(String description, Consumer<String> output,
   Supplier<A> code) {
    final Date before = newDate();
    A iresult = code.get();
    final Long duration = new Date().getTime() - before.getTime();
    output.accept(description + " took " + duration + " milliseconds")

    return result;
  }
}
~~~

Isolation. Assumes that it has no information.

~~~ java
public class TimingTest {
	@Test
	public void testTimed() throws Exception {
		final String description = "Supply carrot";
		AtomicReference<String> output = new AtomicReference();

		Timing.timed(description,
			output::set,
			() -> "carrot");

		assert(output.get().contains(description));
	}
}
~~~

- Specific Types
- Data in, data out
- Tested.

In Java 8. if Main methods were called multiple times, it does not instantiate
example::calculateCost and Example::calculateRevenue multiple times.

Separation of concerns.

~~~ java
//naive, messy way

public CollectionsExample {
	final static String[] food = new String[] {
		"Crunchy carrots",
		"Golden-hued bananas",
		"",
		"Bright oragne pumkins"
		"Little trees of brocolli",
		"meat"
	};

	private static String summerize(final String[] descriptions) {
		final StringBuilder output = new StringBuilder();
		boolean isFirst = true;
		for(String d : descriptions) {
			if(!d.isEmpty()) {
				final String word = lastWord(d);
				if(!isFirst) {
					output.append(" & ");
				}
				output.append(word);
				isFirst = false;
			}
		}
		return output.toString();
	}

	private static String lastWord(final String phrase) {
		final int lastSpace = phrase.lastIndexOf(" ");
		if(lastSpace < 0) {
			return phrase;
		} else {
			return phrase.substring(lastSpace + 1, phrase.length());
		}
	}

	public static void main(final String[] args) {
		final String desiredSummary =
			"carrots & bananas & pumpkins & broccoli & meat";
		System.out.println(summery);
		if (summary.equals(desiredSummary)) System.out.println("yay");
	}
}
~~~

~~~ java
public CollectionsExample {
	final static String[] food = new String[] {
		"Crunchy carrots",
		"Golden-hued bananas",
		"",
		"Bright oragne pumkins"
		"Little trees of brocolli",
		"meat"
	};

	public static final Predicate<String> NON_EMPTY =
		s -> !s.isEmpty();

	private static String summerize(final String[] descriptions) {

		return Arrays.asList(descriptions).stream()
						.filter(NOW_EMPTY)
						.map(lastWord)
						.reduce(joinOn(" & "))
						.orElse("");
	}


	private static BinaryOperator<String> joinOn(String connector) {
		return (allSoFar, nextElement) ->
				allSoFar + connector + nextElement;
	}

	private static BinaryOperator<String> chooseLast =
		(allSoFar, nextElement) ->
			allSoFar + " & " + nextElement;


	private static Function<String, String> lastWord =
		phrase ->
			Arrays.asList(phrase.split(" ")).stream()
			.reduce(chooseLast).orElse("");

	public static void main(final String[] args) {
		final String desiredSummary =
			"carrots & bananas & pumpkins & broccoli & meat";
		System.out.println(summery);
		if (summary.equals(desiredSummary)) System.out.println("yay");
	}
}
~~~

## Streams

Stream = context

Stream is at least as efficient as the loop.

~~~ java
private static String summerize(final String[] descriptions) {
	return Arrays.asList(descriptions).stream()
					.peek(s -> System.out.println("About to filter: ) + s)
					.filter(NOW_EMPTY)
					.peek(s -> System.out.println("About to map : " + s))
					.map(lastWord)
					.peek(s -> System.out.println("About to recue : " + s))
					.reduce(joinOn(" & "))
					.orElse("");
}
~~~

~~~
//output
About to filter: Crunchy carrot
About to map : Crunch carrots
About to reduce : carrots
About to filter : Golden-hued bananas
About to map : Golden-gued bananas
About to reduce: bananas
...
~~~

Streams are lazily processed. Internalized iteration. Reduce does not starts until it is called and
have all the data it needs. It starts when we ask stream to provide value. This is efficient.

External iteration has its own quirks. It's good when you know when to stop, but stream's internal 
iteration can also stop when it is told to.

~~~ java
private static String summerize(final String[] descriptions) {
	return Arrays.asList(descriptions).stream()
					.peek(s -> System.out.println("About to filter: ) + s)
					.filter(NOW_EMPTY)
					.limit(3)
					.peek(s -> System.out.println("About to map : " + s))
					.map(lastWord)
					.peek(s -> System.out.println("About to recue : " + s))
					.reduce(joinOn(" & "))
					.orElse("");
}
~~~

~~~
//output
About to filter: Crunchy carrot
About to map : Crunch carrots
About to reduce : carrots
About to filter : Golden-hued bananas
About to map : Golden-gued bananas
About to reduce: bananas
About to filter : ""
About to filter : "Bright orange pumpkins"
About to map: Bright oragne pumpkins
About to reduce : pumpkins
carrots & bananas & pumpkins.
...
~~~

### Stream operations

~~~ java
public class Sale {
	...

	public double total() {
		return items.stream.mapToDouble(item -> item.price).sum()
}

public class TodaySales {
	static final List<Sale> sales = Arrays.asList(
			new Sale(Store.KANSAS_CITY, new Date(), Optional.of("Sarah"),
				Arrays.asList(
					new Item("carrot", 12.00)
					)),		
			new Sale(Store.ST_LOUIS, new Date(), Optional.empty(),
				Arrays.asList(
						new Item("carrot", 12.00),
						new Item("lizasrd", 99.99),
						new Item("cookie", 1.99)
				)),	
			new Sale(Store.ST_LOUIS, new Date(), Optional.of("Jaime"),
				Arrays.asList(
						new Item("cookie", 1.99)						
						new Item("banana", 99.99),
				)));	

	static Stream<Sale> saleStream() {
		return sales.stream();
	}

	public static void main(String[] args) {
		//how many sales
		long saleCount = saleStream().count();

		// any sales over $100? anyMatch will terminate as soon as it hits one postiive case
		// aka short-circuiting termianal operation
		boolean bigSaleDay = saleStream().anyMatch(sale -> sale.total() > 100);

		// maximum sale amount
		DoubleSummaryStatistics stats = 
			saleStream.mapToDouble(Sale::total).summaryStatistics()
		System.out.println("Max sale amount " + stats.getMax());
		System.out.println("stats on total: " + stats)	
	}
}
~~~

Terminal Operators

- trigger processing
- return a value
- termiante the Stream instance
 
 Steram is kinda like iterator. it is only usable one time.

 ~~~ java

 //COMPILER ERROR 

public static void main(String[] args) {
	//how many sales
	long saleCount = saleStream().count();

	// any sales over $100? anyMatch will terminate as soon as it hits one postiive case
	// aka short-circuiting termianal operation
	DoubleStream totalStream = saleStream().mapToDouble(Sale::total);
	boolean bigSaleDay = totalStream.anyMatch(total -> total > 100.00);
	System.out.println("Big Sale Day? " + bigSaleDay)

	// maximum sale amount

	DoubleSummaryStatistics stats = 
		totalStream.summaryStatistics();
	System.out.println("Max sale amount " + stats.getMax());
	System.out.println("stats on total: " + stats)	
}
~~~

~~~ java
// have to use supplier.
public static void main(String[] args) {
	//how many sales
	long saleCount = saleStream().count();

	// any sales over $100? anyMatch will terminate as soon as it hits one postiive case
	// aka short-circuiting termianal operation

	Supplier<DoubleStream> totalStream = () ->
		saleStream().mapToDouble(Sale::total);
	boolean bigSaleDay = totalStream.anyMatch(total -> total > 100.00);
	System.out.println("Big Sale Day? " + bigSaleDay)

	// maximum sale amount

	DoubleSummaryStatistics stats = 
		totalStream.get().summaryStatistics();
	System.out.println("Max sale amount " + stats.getMax());
	System.out.println("stats on total: " + stats)
~~~

Itermediate Operators

- trigger no processing
- return a Stream
- let you keep going...


~~~ java
// have to use supplier.
public static void main(String[] args) {
	//how many sales
	long saleCount = saleStream().count();

	// any sales over $100? anyMatch will terminate as soon as it hits one postiive case
	// aka short-circuiting termianal operation

	Supplier<DoubleStream> totalStream = () ->
		saleStream().mapToDouble(Sale::total);
	boolean bigSaleDay = totalStream.anyMatch(total -> total > 100.00);
	System.out.println("Big Sale Day? " + bigSaleDay)

	// maximum sale amount
	DoubleSummaryStatistics stats = 
		totalStream.get().summaryStatistics();
	System.out.println("Max sale amount " + stats.getMax());
	System.out.println("stats on total: " + stats)

	// how many items were sold today?
	Supplier<Stream<Item>> itemStream = () ->
		saleStream().flatmap(sale -> sale.items.stream());
	long itemCount = itemStream.get().count();

	// how many different items were sold today? distinct 
	// determines the distinctness by equals method
	long uniqueItemCount = itemStream.get()
		.map(item -> item.identity)
		.distinct().count();
	
	// which different items were sold today? use collect

	// it needs the empty instance of the output type
	// operator to put it into the output type
	// operation to combine two of the output type
	
	List<String> uniqueItemCount = itemStream.get()
		.map(item -> item.identity)
		.distinct()
		.collect(Collectors.toList());

	//or which different items were sold today?
	String uniqueItemCount = itemStream.get()
		.map(item -> item.identity)
		.distinct()
		.collect(Collectors.joining(" & "));

	// summarize sales by store
	Map<Store, List<Sale>> summary = 
		saleStream().collect(Collectors.groupingBy(sale -> sale.store))
	
	// this is more interesting. summarize sales by store
	Map<Store, List<Sale>> summery =
		saleStream().collect(Collectors.groupingBy(sale -> slae.store, 
			Collectors.summarizingDouble(Sale::total)));
	
	summary.keySet().stream().forEach(Store ->
		System.out.println(store + " stats: " + summary.get(store)));
~~~

## Parallel Streams

~~~ java
//source of a many sales
public class RandomSale {

	public static Stream<Sale> streamOf(long qty) {
		return Stream.generate(supplier).limit(qty;
	}

	public static Supplier<Sale> supplier = () new Sale (
		pckAStore(),
		new Date(),
		pickACustomer(),
		randomListOfItems();
	)
}
~~~

~~~ java
	static Stream<Sale> saleStream() {
		return RandomSale.streamOf(10000);
	}

	//lets assume that calculating stats for store is CPU intensive	
	Map<Store, List<Sale>> summery =
		saleStream().parallel()
		.collect(
			Collectors.groupingBy(sale -> Thread.currentThread().getName(),
			Collectors.summarizingDouble(Sale::total)));
			
	summary.keySet().stream().sorted().forEach(Store ->
		System.out.println(store + " stats: " + summary.get(store)));
~~~

## Optional

super useful during funtional programnming. 

~~~ java
	
	static Stream<Sale> saleStream() {
		return RandomSale.streamOf(3);
	}

	static Optional<Sale> findSaleOf(String itemName) { 
		return saleStream.filter(sale ->
			sale.items.stream().antMatch(item ->
			item.identity.equals(itemName))))
			.findFirst();
	}

	// Customer who bought a carrot
	static Optional<String> carrotCustomer() {
		return findSaleOf("carrot").flatmap(sale -> sale.customer);
	}

	// store that sold a store
	return Optional<Store> carrotStore() {
		return findSaleOf("carrot").map(sale -> sale.store);
	}
~~~

Pass code in context

if a context object is a monad if it has

- map
- flatMap
- wrapping constructor

collect is also as known as fold