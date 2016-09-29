# Intro to Testing in Java

## Why automate test?

- Detecting the problem first, preferably before they become too expensive to fix
- Repeat the tests in order to achieve ^

Testing

- Achieves stakeholders goals
- Meets functional requirements
- Correctly Handles corner cases

Automating Test

- Deliver Reliability & Quality
- Remove fear of change. You can chagne source code easily

## The Testing Hierarchy

- System Test
	- "End to End" test
	- check that application meets its requirements
	- Slowest, least birttle
	- requires the ability to run the application
- Aggregate Test
	- does a component do its job?
	- eg. Persistence repository can list saved albums
	- slower, less brittle.
- Unit Test
	- Testing just a single unit of funcionality
	- No non-trivial dependencies

# Testing Code

## CODE

Cafe

- class for brewing coffeee -- including managing beans and milk

CoffeeType

- enum of different types of coffee

Coffee

- a class for a single brewed cup

~~~ java 
//CoffeeType.java
package com.monotonic.testing.m2;

/**
 * Describes the type of coffee that we're going to serve.
 */
public enum CoffeeType {
    Espresso(7, 0),
    Latte(7, 227),
    FilterCoffee(10, 0);

    private final int requiredBeans;
    private final int requiredMilk;

    CoffeeType(int requiredBeans, int requiredMilk) {
        this.requiredBeans = requiredBeans;
        this.requiredMilk = requiredMilk;
    }

    public int getRequiredBeans() {
        return requiredBeans;
    }

    public int getRequiredMilk() {
        return requiredMilk;
    }
}
~~~

~~~ java
//Coffee.java

package com.monotonic.testing.m2;

/**
 * An individual coffee that has been served.
 */
public final class Coffee {

    private final CoffeeType type;
    private final int beans;
    private final int milk;

    public Coffee(CoffeeType coffeeType, int beans, int milk) {
        this.type = coffeeType;
        this.beans = beans;
        this.milk = milk;
    }

    public CoffeeType getType() {
        return type;
    }

    public int getBeans() {
        return beans;
    }

    public int getMilk() {
        return milk;
    }

    @Override
    public String toString() {
        return "Coffee{" +
                "type=" + type +
                ", beans=" + beans +
                ", milk=" + milk +
                '}';
    }
}

~~~

~~~ java
//Cafe.java 

package com.monotonic.testing.m2;

/**
 * A coffee shop that can brew coffee and maintains an internal stock of beans and milk.
 */
public final class Cafe {

    private int beansInStock = 0;
    private int milkInStock = 0;

    public Coffee brew(CoffeeType coffeeType) {
        return brew(coffeeType, 1);
    }

    public Coffee brew(CoffeeType coffeeType, int strength) {
        requirePositive(strength);

        int requiredBeans = coffeeType.getRequiredBeans() * strength;
        int requiredMilk = coffeeType.getRequiredMilk() * strength;
        if (requiredBeans > beansInStock || requiredMilk > milkInStock) {
            throw new IllegalStateException();
        }

        beansInStock -= requiredBeans;
        milkInStock -= requiredMilk;
        return new Coffee(coffeeType, requiredBeans, requiredMilk);
    }

    public void restockBeans(int weightInGrams) {
        requirePositive(weightInGrams);
        beansInStock += weightInGrams;
    }

    public void restockMilk(int weightInGrams) {
        requirePositive(weightInGrams);
        milkInStock += weightInGrams;
    }

    private void requirePositive(int value) {
        if (value < 1) {
            throw new IllegalArgumentException();
        }
    }

    public int getBeansInStock() {
        return beansInStock;
    }

    public int getMilkInStock() {
        return milkInStock;
    }
}

~~~

~~~ java
//CafeTest.java

package com.monotonic.testing.m2;

import org.junit.Test;

import static com.monotonic.testing.m2.CoffeeType.Espresso;
import static com.monotonic.testing.m2.CoffeeType.Latte;
import static org.junit.Assert.assertEquals;

public class CafeTest {

    @Test
    public void canBrewEspresso() {
        // given
        Cafe cafe = new Cafe();
        cafe.restockBeans(7);

        // when
        Coffee coffee = cafe.brew(Espresso);

        // then
        assertEquals(7, coffee.getBeans());
        assertEquals(0, coffee.getMilk());
        assertEquals(Espresso, coffee.getType());
    }
} 

~~~

## Running Test via Command line 

~~~ xml 
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.monotonic</groupId>
    <artifactId>IntroductionToTestingInJavaModule2</artifactId>
    <version>1.0-SNAPSHOT</version>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>
</project>
~~~

~~~ 
> mvn clean test
~~~

## The Common Structure of Tests

Given

- What should the world look like when the behaviour happens?
- The preconditions for the test

When

- What is being tested
- The behavior

Then

- What are the changes that happened?  

~~~ java
//CafeTest.java
...
    @Test
    public void brewingEspressoConsumesBeans() {
        // given
        Cafe cafe = new Cafe();
        cafe.restockBeans(7);

        // when
        cafe.brew(Espresso);

        // then
        assertEquals(0, cafe.getBeansInStock());
    }
...
~~~

## Exception, Failures, and Errors

- @Test(expected = IllegalArgumentException.class
- Failure
    - tried to check a behavior
    - an assertion failed
    - suggestrs the code is broken
- Error
    - at any point in the test
    - an exception is thrown
    - Suggests the test is broken

~~~ java
//CafeTest.java

...
@Test(expected = IllegalStateException.class)
public void lattesRequiresMilk() {
    // Given
    Cafe cafe = new Cafe();
    cafe.restockBeans(7);

    //when
    cafe.brew(CoffeeType.Latte);
}
~~~