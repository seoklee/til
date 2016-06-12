# Anotation

Annotation is about metadata, data about the code. Metadata will not directly effect code. Adding more information about the code.

## What are thoseee
- Data holding class
- Applied to...
	- classes
	- fields
	- methods

- Syntax
~~~ java
public @interface ContractAnnotation
{
	String signature();
	String date();
}
~~~ 

~~~ java
@SurppressWarnings(value='deprecation')
~~~

- Uses (Why?)
	- Giving a compiler hint.
		- @Deprecated
		- @Override
		- @SurpressWarning
	- Tools
		- Generate code
		- Documentation
		- Testing Frameworks
	- Runtime
		- Serialization
		- IoC
			- inversion of control
				- inserting dependency easily
		- ORM

Built-in annotation

~~~ java
public class Printer {
	...
	
	@Deprecated
	public void printColor() {
		...
	}
	
}

public class HelloWorld {
	public static void main(String[] args) {
		Printer<ColorCartridge> printer = new Printer<ColorCartridge>(...);
		
		printer.loadPaper(5);
		//this will throw compiler warning for deprecation
		printer.printColor();
	}
}
~~~

## Creating your own annotation

~~~ java
//annotation. retention makes the annotation stick even after compilation of the object
@Retention(RetentionPolicy.RUNTIME)
public @interface PrintingDevice 
{
	String defaultPrintMethod();
	int defaultNumberOfCopies();
}

@PrintingDevice(defaultPrintMethod = "print", defaultNumberOfCopies = 5)
public class Printer<T extends ICartridge> implements iMachine
{
	....
}
~~~

## Reflection

Code that can examine itself and even change itself at runtime.

Metaprogramming! Code treating code like data.

Reflection is not efficient. It's another observation at runtime!


Again.. this is not the best example of using annotation.
~~~ java

public class HelloWorld {
	public static void main(String[] args) {
		Printer<ColorCartridge> printer = new Printer<ColorCartridge>(...);
		
		printer.loadPaper(10);
		
		//pretend you do not know what print method... this returns printer class
		PrintingDevice annotation = printer.getClass().getAnnotation(PrintingDevice.class);
		
		//getting method
		try {
			Method printMethod = printer.getClass().getMethod(annotation.defaultPrintMethod(), int.class);
		} catch (...) {
		
		}
		
		//calling method
		try {
			printMethod.invoke(printer, annotation.defaultNumberOfCopies());
		} catch (...) {
		
		}
		
		printer.outputPage(4);
	}
}
~~~