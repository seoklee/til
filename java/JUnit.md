#JUnit

JUnit is a unit testing framework for Java

- Usually you need a separate test class

~~~ java
public class TrackingServiceTest {
	public void test() {
		assertEquals(...)
	}
}
~~~

## JUnit Features

- Asserts
	- What you expect and what you get.
- Test setup and teardown
	- Before test runs and afte the test
- Exception testing
- Test Suites
	- Managing large amount of test
- Parameterized Testing
	- Create test that acts upon the data that were supplied.
- Assumptions
	- ignore tests that are not qualified to run in certain platforms
- Rules
	- Extends functionalities of JUnit by adding behaviours to tests if we use a particular rules
		- Creating a file when the test is run...
- Theories
	- Special tests on the different contexts. Set them up in different data, and see if these theories holds
- Integration with other popular builds tools
	- Ants and Maven

## Architecture & Unit Testing

~~~
JUnit
	|
Test Code 	--> 		SUT (Subject Under Test)
	|
JUnit Runner (IDE)
~~~

--- 

Unit testing is simply take the smallest code you can test on and test it. In Java, it's a class.

## JUnit History

Kent Back is creator of agile movement, extreme programming, and JUnit.

- 1994, SUnit Created 
- 1997, The JUnit Flight 
- 2000, JUnit.org
- 2002, Eclipse IDE Support

## Unit Testing w/ JUnit

Layout

~~~ 
ProteinTracker
	- src
		- com.simpleprogrammer.project
			- HistoryItem.java
			- TrackingService.java
ProteinTrackerTest
	- src
		- (default package)
			- HelloJUnitTest.java
			- TrackingService Tests
	- JRE System Library
	- JUnit 4
		- junit.jar
		- ...
~~~

~~~ java
import static org.junit.Assert.*;

public class HelloJUnitTest() {
	
	@Test
	public void {
		//fail("Not Yet Implemented);
	}
}
~~~

## JUnit Test Methods

- public
- return void
- @Test
- Make test name descriptive as possible for clarity!

## Junit Annotations

- @Test
	- Indicating it is a test method
- @Before
	- Some behaviour before test
- @After
	- Some behaviour before test
- @BeforeClass
	- Before any methods in the test class is executed
- @AfterClass
	- After any methods in the test class is executed
- @Ignore
	- Ignore test for JUnit runner
- @Test(expected = Exception.class)
	- Expect an exception to be thrown.
- @Test(timeout = 100)
	- self-explanatory


~~~ java
import org.junit.Test;

import com.simpleprogrammer.proteintracker.TrackingService;
import static org.junit.Assert.*;


public class TrackingServiceTests() {
	
	@Test
	public void NewTrackingServiceTotalIsZero() 
	{
		TrackingService service = new TrackingService();
		assertEquals("Tracking Service Total was not zero", 0, service.getTotal());
	}
	
	@Test
	public void whenAddingProteinTotalIncreasesByThatAmount()
	{
		TrackingService service = new TrackingService();
		service.addProtein(10);
		assertEquals("Protein amount was not correct", 10, service.getTotal());
	}
	
	@Test
	public void whenRemovingProteinTotalDecreaseByThatAmount()
	{
		TrackingService service = new TrackingService();
		service.removeProtein(5);
		assertEquals(0, service.getTotal());
	}
}
~~~

## Before and After

Really useful for removing duplications. Be mindful that test does not depends on the order!

Teardown is pretty useful if you want to roll back DB changes that you did during the testing. But Setup is mostly used.


~~~ java
import org.junit.Test;

import com.simpleprogrammer.proteintracker.TrackingService;
import static org.junit.Assert.*;


public class TrackingServiceTests() {
	
	@Before
	public void setUp()
	{
		System.out.println("Before");
		service = new TrackingService();
	}
	
	@After
	public void tearDown()
	{
		System.out.println("After");
	}
	
	@Test
	public void NewTrackingServiceTotalIsZero() 
	{
		assertEquals("Tracking Service Total was not zero", 0, service.getTotal());
	}
	
	@Test
	public void WhenAddingProteinTotalIncreasesByThatAmount()
	{
		service.addProtein(10);
		assertEquals("Protein amount was not correct", 10, service.getTotal());
	}
	
	@Test
	public void WhenRemovingProteinTotalDecreaseByThatAmount()
	{
		service.removeProtein(5);
		assertEquals(0, service.getTotal());
	}
}
~~~

## BeforeClass & AfterClass

Use d when if you want to execute before any test and execute after all the tests. They have to be static, though.

Useful when you want to setup some test data first, and cleaning up afterward. They are useful for non-testing purpose of JUnit.

~~~ java
public class TrackingProteinTest
{
	private TrackingService service;
	@BeforeClass
	public static void before() 
	{
		System.out.println("Before class");
	}
	@AfterClass
	public static void after()
	{
		System.out.println("After class");
	}
	

}
~~~

## Ignore

Pretty self-explanatory. Pretty useful for debugging purpose.

~~~ java
public class TrackingProteinTest
{
	private TrackingService service;
	@BeforeClass
	@Ignore
	public static void before() 
	{
		System.out.println("Before class");
	}
	@AfterClass
	public static void after()
	{
		System.out.println("After class");
	}
}
~~~

## Exception Testing

@Test(expected=Exception.class)

Test will only pass if that exception is thrown.

~~~ java
public class TrackingService throws InvalidGoalException {
	...
	
	public void setGoal(int value) {
		if(value < 0)
			throw new InvalidGoalException();
		goal = value;
	}
	
	...
}

---

public class TrackingProteinTest
{
	...
	
	@Test(expected = InvalidGoalException.class)
	public void WhenGoalIsSetToLessThanZeroExceptionIsThrown()
	{
		service.setGoal(-5);
	}
}
~~~

## Timing Out

@Test(timeout=100)

Speicify the test time in milliseconds when the test completes.

~~~ java
public class TrackingProteinTest
{
	...
	
	@Test(timeout = 200)
	public void BadTest()
	{
		for(int i = 0; i < 10000000; i++)
			service.addProtein(1);
	}
}
~~~

This test fails!

There is no way to global timeout, though. Use it on a specific test that will take up a long time.

## Assertions (Basic)

- assertArrayEquals
- assertEquals
- assertTrue
- assertFalse
- assertNull
- assertNotNull
- assertSame
- assertNotSame
- fail


# JUnit Advance

## Test Suites

Running multiple test classes

~~~
@RunWith(Suite.class)
@Suite.SuitClasses(...)

								| -> Test Class
Test Suites 					---> Test Class
								| -> Test Class
~~~

~~~ java
import org.junit.runner.RunWith;
import org.junit.runners.Suite;

@RunWith(Suite.class)
//this below annotation is supported after JUnit 4
@Suite.SuiteClasses({
	HelloJUnitTest.class,
	TrackingServiceTests.class
})
public class ProteinTrackerSuite {
	
}
~~~

## Categories

Similar to Test Suites.

Category runner is a specialized suites runner that is able to read category annotation off test methods and test classes, and determine whether or not to run containing tests depending on categories match those of test suites.


~~~
@RunWith(Categories.class)
@IncludeCategory(A.Class)
										@Category(A.class)
									| -> Test Class
									|	
									|	@Category(A.class)
Test Suites 						---> Test Class
									|
									|												|	@Category(B.Class)
									| -> Test Class
~~~


~~~java

public interface GoodTestsCategory {

}

---

public interface GoodTestsCategory {

}
---

public class TrackingServiceTests() {
	
	@Before
	public void setUp()
	{
		System.out.println("Before");
		service = new TrackingService();
	}
	
	@After
	public void tearDown()
	{
		System.out.println("After");
	}
	
	@Test
	public void NewTrackingServiceTotalIsZero() 
	{
		assertEquals("Tracking Service Total was not zero", 0, service.getTotal());
	}
	
	@Test
	@Category({GoodTestCategory.class, BadCategory.class})
	public void WhenAddingProteinTotalIncreasesByThatAmount()
	{
		service.addProtein(10);
		assertEquals("Protein amount was not correct", 10, service.getTotal());
	}
	
	@Test
	@Category(GoodTestCategory.class)
	public void WhenRemovingProteinTotalDecreaseByThatAmount()
	{
		service.removeProtein(5);
		assertEquals(0, service.getTotal());
	}
	
	@Test(expected = InvalidGoalException.class)
	@Category(GoodTestCategory.class)
	public void WhenGoalIsSetToLessThanZeroExceptionIsThrown()
	{
		service.setGoal(-5);
	}
	
	
	@Test(timeout = 200)
	public void BadTest()
	{
		for(int i = 0; i < 10000000; i++)
			service.addProtein(1);
	}
}

---


@RunWith(Categories.class)
@IncludeCategory(GoodTestsCategory.class)
@ExcludeCategory(BadCategory.class) 
@Suite.Suiteclasses({
	HelloJUnitTest.class,
	TrackingServiceTests.class
})
public class GoodTestSuite {

}
~~~

This will run two tests. It includes test classes that were included in IncludeCategory and excludes all those works that were excluded in ExcludeCategory.


## Parameterized Tests

Useful for repeative tests that only vary in input and expected output. We can inject input and expected result into the test class without creating 50 different tests.

We have to create a static methods.

~~~ java
import java.util.Arrays;
import java.util.List;

import org.junit.runner.RunWith;
import org.junit.runners.Parameterized;
import org.junit.runners.Parameterized.Parameters;
import static org.junit.Assert.*

@RunWith(Parameterized.class)
public class ParameterizedTests {
	
	private static TrackingService service = new TrackingService();
	private int input;
	private int expected;
	
	@Parameters
	public static List<Object[]> data() {
		return Arrays.asList(new Object[][]) {
			{5, 5},
			{5, 10},
			{-12, 0},
			{50, 50},
			{1, 51}
		}};	
	} 
}

	public ParameterizedTests(int input, int expected) {
		this.input = input;
		this.expected = expected;
	}
	
	@Test
	public void test() {
		if(input >= 0)
			service.addProtein(input);
		else
			serviceRemoveProtein(-input);
		
		assertEquals(expected, service.getTotal());
	}
~~~

## Advanced Assertions

AssertThat([value],[matcherstatement])

MatcherStatement

- is
- HasItem
- ContainsString
- IsNull
- AllOf

~~~ java
TracingServiceTest.java

...
import static org.hamcrest.CoreMatchers.*;
import org.junit.matchers.JUnitMatchers.*;

...

public class TrackingServiceTests() {

	@Test
	@Category({GoodTestCategory.class, BadCategory.class})
	public void WhenAddingProteinTotalIncreasesByThatAmount()
	{
		service.addProtein(10);
		assertEquals("Protein amount was not correct", 10, service.getTotal());
		assertThat(service.getTotal(), is(10));
		//two asserts are identical. last one is more legible.
		
		asssertThat(service.getTotal(), allOf(is(10), instanceOf(Integer.class)));
	}
}

~~~

## Advanced Exception Testing

~~~ java
@Rule
Expected Exception
		|
		|
	@Test
	Test Method
~~~

You can also check the exception messages

~~~ java
public class TrackingService throws InvalidGoalException {
	...
	
	public void setGoal(int value) {
		if(value < 0)
			throw new InvalidGoalException("Goal was less than zero!");
		goal = value;
	}
	
	...
}

--- 

public class InvalidGoalException extends Exception {
	
	public InvalidGoalException(String message) {
		super(message);
	}
}

--- 

public class ParameterizedTests {
	
	...
	
	@Rule
	public ExpectedException thrown = ExpectedExcpetion.none();
	
	@Test
	public void WhenGoalIsSetToLessThanZeroExceptionIsThrown()
	{
		thrown.expect(InvalidGoalException.class);
		thrown.expectMessage("Goal was less than zero!");
		thrown.expectMessage(containString("Goal"));
		service.setGoal(-5);
	}
	
	... 
}
~~~

## Rules

JUnit has several rules.

- TemporaryFolder
- ExternalResource
- ErrorCollector
- Verifier
- TestWatcher
- TestName
- Timeout
- ExpectedException
- you can create your own rule!

Error Collector can collect errors and display them at once, not quitting on it.

Timeout rule can actually set a global timeout for a test set.

RuleChain allows chain multiple rules together

@ClassRule can create rules that work in class level.


~~~ java
public class ParameterizedTests {
	
	...
	
	@Rule
	public Timeout timeout = new Timeout(20)();
	
	@Test
	public void BadTest()
	{
		for(int i = 0; i < 10000000; i++)
			service.addProtein(1);
	}
	
	@Test
	@Category({GoodTestCategory.class, BadCategory.class})
	public void WhenAddingProteinTotalIncreasesByThatAmount()
	{
		service.addProtein(10);
		assertEquals("Protein amount was not correct", 10, service.getTotal());
		assertThat(service.getTotal(), is(10));
		//two asserts are identical. last one is more legible.
		asssertThat(service.getTotal(), allOf(is(10), instanceOf(Integer.class)));
		for(int i = 0; i < 10000000; i++)
			service.addProtein(1);
	}
}
~~~

With that rule those two tests will fail.

## Theories

Basically glamorized tests. Theories are tests that holds true based on certain assumptions.

use @Theory annotation. Use a single parameter of any data type you like, and will use it for test. Theory will have same expected result for all results.

Theory that if you add protein, it will result in a positive value.

~~~ java
import org.junit.experimental.theories.DataPoints;
import org.junit.experimental.theories.Theories;
import org.junit.runner.RunWith;

import static org.junit.Assert.*;

@RunWith(Theories.class)
public class TrackingServiceTheories {
	
	@DataPoints
	public static int[] data() {
		return new int[] {
			1, 5, 10, 15, 20, 50, -4
		};
	}
	
	@Theory
	public void positiveValuesShouldAlwaysHavePositiveTotals(int value) {
		TrackingService service = new TrackingService();
		service.addProtein(value);
		Assume.assumeTrue(value > 0);
		
		assertTrue(service.getTotal() > 0);
	}
}
~~~ 

That Assume.assumeTrue will ignore any points that does not meet the condition.

