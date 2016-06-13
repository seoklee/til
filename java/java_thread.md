#Threading in Java


## What is Thread?

- Independent path of execution...
- Threads share memory
	- Processes do not

## Why Threads?

- Multithreads does not lead in increase in performance.
- Good use of thread is splitting the work in parallel, or to do 'some task' in background.

## 'Why Thread' Example

- Webserver
	- Multiple clients' reqeusts have to queue themselves if a webserver is single-threaded
- Responsive UI
	- 'freezing' effect, because resource is caught up with doing another request.

## Real Code for Threading

~~~ java
public class ContinousPrinter extends Threads 
{
	private Printer<ICartridge> printer =
		 new Printer<ICartridge>(True, "IPrint", ColorCartridge.Blue);
	
	@Override
	public void run()
	{
		for(int i = 0; i < 100; i++) 
			printer.printUsingCartridge(ColorCartridge.BLUE, "In Thread " + i);
		super.run();
	}

}

public class HelloWorld {
	public static void main(String [] args) {
		ContinuousPrinter cp = new ContinuousPrinter();
		cp.start();
		
		for(int i = 0; i < 100; i++) 
		{
			System.out.println("Main Thread " + i);
		}
	}
}
~~~

This snip of code output something like...

~~~
In Thread 1
Main Thread 1
In Thread 2
Main Thread 2
...
~~~

meaning this is approximately running at the same time.

This way, however, has few problems.

- Since ContinousPrinter extends threads, it cannot inherit other objects.
- It's not really dynamic. (not reusable)
	- It would be better to use interface. 
		- you can implement 'Runnable'

~~~ java
public class ContinousPrinter implements Runnable
{
	private Printer<ICartridge> printer =
		 new Printer<ICartridge>(True, "IPrint", ColorCartridge.Blue);
	
	@Override
	public void run()
	{
		for(int i = 0; i < 100; i++) 
			printer.printUsingCartridge(ColorCartridge.BLUE, "In Thread " + i);
		super.run();
	}

}

public class HelloWorld {
	public static void main(String [] args) {
		ContinuousPrinter cp = new ContinuousPrinter();
		Thread thread = new Thread(cp);
		thread.start();
		for(int i = 0; i < 100; i++) 
		{
			System.out.println("Main Thread " + i);
		}
	}
}
~~~

## Executor

Create thread and execute thread using executor pattern

~~~ java
public class HelloWorld {
	public static void main(String [] args) {
		ContinuousPrinter cp = new ContinuousPrinter();
		
		//allow maximum of 3 threads at the same time
		ExecutorService executor = Executors.newFixedThreadPool(6);
		
		executor.submit(cp);
		executor.submit(cp);
		executor.submit(cp);
		executor.submit(cp);
		executor.submit(cp);
		executor.submit(cp);
		executor.shutdown();
		
		
		for(int i = 0; i < 100; i++) 
		{
			System.out.println("Main Thread " + i);
		}
	}
}
~~~

~~~
In Thread 0
In Thread 0
Main Thread 0
In Thread 0
In Thread 1
Main Thread 1
In Thread 0
In Thread 0
...
~~~

## Concurrency Issues

- Synchronization

Two different threads accessing the same object, and it can cause some trouble. One way to absolve it is atomic action.

Make the method 'synchronized,' it makes one thread can execute this method

~~~ java
	public synchronized <U extends ICartridge> void printUsingCartridge(U cartridge, String message)
	{
		System.out.println("Entered " + Thread.currentThread().getId());
		System.out.println(message);
		System.out.println("Exiting " + Thread.currentThread().getId());
	}
~~~

- Deadlock and Starvation

