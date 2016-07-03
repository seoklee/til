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

	
	private staticv BinaryOperator<String> joinOn(String connector) {
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
