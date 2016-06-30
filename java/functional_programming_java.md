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
