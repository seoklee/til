# Understanding the Java Virtual Machine : Class Loading and Reflection

## Intro 

- Intro to classloading
- Java code compiles to .class files
    - these classes have to be loaded into the VM
- Where does Java loads its classes from?
    - what is the classpath
- Setting the classpath globally
- Setting the classpath when running a Java app.

~~~ java
//Helper.java
package com.mantiso;

public class Helper {
    public String getMessage() {
        return "Hello from helper";
    }
}

//Main.java
package com.pluralsight;

public class Main {
    public static void main(String[] args) {
        com.maintiso.Helper helper = new com.mantiso.Helper();
        System.out.println(helper.getMessage());
    }
}
~~~

~~~ 
> javac -d classes -sourcepath src src/com/mantiso/Helper.java
> javac -d classes -sourcepath src src/com/pluralsight/Main.java
> java classes/com/pluralsight.Main.class
Error
> java classes/com.pluralsight.Main.class
Error
> set CLASSPATH=classes
> java com.pluralsight.Main
Hello From Helper
> set CLASSPATH=
> java com.pluralsight.Main
Error
> java -cp classes com.pluralsight.Main
Hello From Helper
> cd classes
> jar cvf helper.jar com/mantiso/Helper.java
> mv helper.jar ../lib
> cd ..
> rm classes/com/mantiso/Helper.java
> java -cp classes com.pluralsight.Main
Error
> java -cp classes:lib/helper.jar com.pluralsight.Main
Hello From Helper
~~~

### Will cover

- Classloading basic
- Writing our own classloader
- using classloading delegation
- Hot deploying Java code
- Understanding Java reflection
- Using reflection to implment a simple IoC container.

## Basics of Classloading

4 aspects

- Core classes
    - Shipped to JVM
    - String, math classes... etc

- Extension classes
    - Cryptography classes

- Delegation model
    - when a classloader load a class, it delegate its core to it's parent class loader.. and cascade through. 

- Displaying the delegation
    

## Core java classes

~~~
> java -cp classes:lib/helper.jar com.pluralsight.Main
Hello From Helper
> mv lib/helper.jar lib/foo.jar
> java -cp classes:lib/helper.jar com.pluralsight.Main
Error
~~~

classpath is where java loads its classes from

JRE has a lib directory with rt.jar where base classes are contained. (java.lang, java.util., etc)

inside lib directory, ext directory contains those extension classes (cryptography classes, etc.)

~~~
> mv lib/helper.jar somewhere_in_jaba/JRE/lib/ext/
> java -cp classes com.pluralsight.Main
Hello From Helper
> javac -sourcepath src src/com/pluralsight/Main.java
//JRE and JDK can use different ext folders. BE CAREFUL. compiling using the extension folder in JDK 
> java -cp classes -Djava.ext.dirs=./lib com.pluralsight.Main
Hello from Hellper
~~~


~~~ java
//Main.java
package com.pluralsight;

public class Main {
    public static void main(String[] args) {
        com.maintiso.Helper helper = new com.mantiso.Helper();
        System.out.println(helper.getMessage());
        String message = new String("Hello World");
        System.out.println(message);
    }
}
~~~

~~~
> javac -sourcepath src src/com/pluralsight/Main.java
> java -cp classes com.pluralsight.Main
Hello from Helper
Hello World
//getting String from rt.jar 
~~~

~~~ java
//src/java/lang/String.java

package java.lang;

public class String {}
~~~

~~~
> javac -sourcepath src src/com/pluralsight/Main.java
Errors (Constructors not found)
~~~

if we change..

~~~ java
//src/java/lang/String.java

package java.lang;

public class String {
    public int getValue() {return 42;}
}

//Main.java
package com.pluralsight;

public class Main {
    public static void main(String[] args) {
        com.maintiso.Helper helper = new com.mantiso.Helper();
        System.out.println(helper.getMessage());
        String message = new String();
        System.out.println(message.getValue());
    }
}
~~~

~~~
> javac -sourcepath src src/com/pluralsight/Main.java
> java -cp classes com.pluralsight.Main
Hello From helper
Errors (No such method. No getValue method)
~~~

So compilation works using the normal java.lang.String. Bootstrap class loader loads those core classes. You can override bootstrap class loader.

## Delagation

More than a class loader. There is a classloader that loads base class, a classloader that loads extension classes

There is a hierarchy of classloaders. classloader has parent except base classloader. 

Classloader may day delegate to its parent

Parent may load class

If you load very basic java class...

~~~
Boostrap class loader (written in C, when using Oracle JVM)
    ^ asked to load class   |      Success or fail
    |                       0
Extension class loader 
    ^                       |      Success or fail
    | asked to load class   0 
Application class loader
    |                       |
    asked to load class     0
~~~

When a class is loaded, it won't be loaded again. It will be one instance of the class. (kinda like singleton.)

~~~ java
package com.pluralsight;

import java.net.URL;
import java.net.URLClassLoader;

public class Delegation {
    public static void main(String[] args) {
        URLClassLoader classLoader = (URLClassLoader) ClassLoader.getSystemClassLoader();
        do{
            System.out.println(classLoader);
        } while ((classLoader = (URLClassLoader) classLoader.getParent()) != null);

        System.out.println("Bootstrap classloader"); 
    }
}
~~~


~~~ 
> javac -sourcepath src src/com/pluralsight/Delegation.java -d classes
> java -cp classes com.pluralsight.Delegation
sun.misc.Launcher@AppClassLoader
sun.misc.Launcher@ExtClassLoader
Bootstrap classloader
~~~

~~~ java
package com.pluralsight;

import java.net.URL;
import java.net.URLClassLoader;

public class Delegation {
    public static void main(String[] args) {
        URLClassLoader classLoader = (URLClassLoader) ClassLoader.getSystemClassLoader();
        do{
            System.out.println(classLoader);
            for(URL url : classLoader.getURLs()) {
                System.out.printf("\t %s\n", url.getPath());
            }
        } while ((classLoader = (URLClassLoader) classLoader.getParent()) != null);
        System.out.println("Bootstrap classloader"); 
    }
}
~~~

~~~ 
> javac -sourcepath src src/com/pluralsight/Delegation.java -d classes
> java -cp classes com.pluralsight.Delegation
sun.misc.Launcher@AppClassLoader
    /demo/classes
sun.misc.Launcher@ExtClassLoader
    java_path/jre/lib/ext/*.jar
Bootstrap classloader
~~~

## Writing and Using a URLClassLoader

~~~ java
package com.pluralsight;

public class Main {
    public static void main(String[] args) {
        Url url;
        try {
            url = new URL("file:///c:/demos/libs/quoter.jar")
            URLClassLoader ucl = new URLClassLoader(new URL[]{url});
            Class clazz = ucl.loadClass("com.mantiso.Quote");
            Object o = classNewINstance();
            System.out.println(o.toString()):
        } catch (MalformedURLException e) {
            e.printStackTrace
        } catch(ClassNotFoundException e) {
            e.printStackTrace();
        } catch(InstantiationException e) {
            e.printStackTrace();
        } catch(IllegalAccessException e) {
            e.printStackTrace();
        }
    }
}
~~~

~~~
javac -cp out/production/demos com.pluralsight.Main
com.mantiso.Quote@hashcode
~~~

## Using Interfaces and Implementation Classes.

~~~
Implementation
    |- src
        |- com.pluralsight
            |-> Quote

Interfaces
    |- src
        |- com.pluralsight
            |-> IQuote
~~~

~~~ jar

package com.pluralsght 

public class Quote {}
~~~

~~~ java
package com.pluralsight;

public class Quote implements IQuote {
    @Override
    public String getQuote() {
        return "A rolling Stone gathers no moss";
    }
}
~~~

~~~ java
package com.pluralsight;

public class Main {
    public static void main(String[] args) {
        Url url;
        try {
            url = new URL("file:///c:/demos/ClassloadingWithItf/out/artifacts/Implementation_jar/Implementations.jar")
            URLClassLoader ucl = new URLClassLoader(new URL[]{url});
            Class clazz = ucl.loadClass("com.pluralsight.Quote");
            IQuote quote = (IQuote) clazz.newInstance();
            System.out.println(quote.getQuote()):
        } catch (MalformedURLException e) {
            e.printStackTrace
        } catch(ClassNotFoundException e) {
            e.printStackTrace();
        } catch(InstantiationException e) {
            e.printStackTrace();
        } catch(IllegalAccessException e) {
            e.printStackTrace();
        }
    }
}
~~~

Build artifacts where we have implementation.jar and interfaces.jar

~~~
> java -cp out/production/Client;out/artifacts/Interfaces_jar/Interfaces.jar com.mantiso.Main
A rolling Stone gathers no moss.
~~~

- if using our own classloader
    - cannot reference implementation in the code
    - this will cause the class to be loaded with the app class loader
- split code into implementation and interface
    - interface definition go on the classpath
    - implementation is loaded by our classloader

~~~ java
//running tomcat locally and move jar file to the root of the server. (..webapps/ROOT)
public class Main {
    public static void main(String[] args) {
        Url url;
        try {
            url = new URL("http://localhost:8080/Implementation.jar")
            URLClassLoader ucl = new URLClassLoader(new URL[]{url});
            Class clazz = ucl.loadClass("com.pluralsight.Quote");
            IQuote quote = (IQuote) clazz.newInstance();
            System.out.println(quote.getQuote()):
        } catch (MalformedURLException e) {
            e.printStackTrace
        } catch(ClassNotFoundException e) {
            e.printStackTrace();
        } catch(InstantiationException e) {
            e.printStackTrace();
        } catch(IllegalAccessException e) {
            e.printStackTrace();
        }
    }
}
~~~

You can load classes over network, too.

## Writing our own classloader

- will not load all classes
    - delegate to the 'system' classloader
- if the parent can't load
    - load the class bytes from the db.
    - Use classloader's defineClass method to load the class
- Example uses SQL Server

~~~ java
public class Main {
    public static void main(String[] args) {
        Url url;
        try { 
            SQLServerClassLoader cl = new SqlServerClassLoader("jdbc:sqlserver://localhost...")
            Class clazz = cl.findClass("com.pluralsight.Quote");
            IQuote quote = (IQuote) clazz.newInstance();
            System.out.println(quote.getQuote()):
        } catch (MalformedURLException e) {
            e.printStackTrace
        } catch(ClassNotFoundException e) {
            e.printStackTrace();
        } catch(InstantiationException e) {
            e.printStackTrace();
        } catch(IllegalAccessException e) {
            e.printStackTrace();
        }
    }
}
~~~


