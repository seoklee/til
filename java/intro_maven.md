#Introduction to Maven

Mainly, THE role of Maven is dependency management and transitive dependency.

## Overview

At simpliest, it's a build tool.

- produces one artifact (component) whether it be a zip, war, jar file.
- Helps us manage dependencies.

Can be used as a project management tool

- Handles versioning and releases (since it only produces only one artifact...)
- Easily produce Javadocs as well as other site information

### Why?

- Repeatable builds
	- can recreate our build for any environment
- Transitive Dependencies
	- Downloading a dependency with also pull other items it needs
- Contains everything it needs for your environment
- Works with local repo
- A preferred choice for a build tools like Jenkils or Cruise Control

### Ant vs Maven

Ant

- Ant is developed to be a replacement for a build tool called Make
- designed to be cross platform
- built on top of Java and XML
- Explicitely do everything in Ant
~~~
<target name="clean" description="cleanup">
	<!-- Delete the ${build} and ${dist} directory trees -->
	<delete dir="${build}" />
</target>
~~~
- No standard
- define everything that we want to do
	- clean, clear, cleanup, etc....

Maven

- A full fledged build tool
- A lot of implicit functionality
- Consistency across projects
- also capable to achieve inheritance in projects
- Transitive dependencies
- Built around versioning

Maven

- Can be a black box
- Steeper learning curve
- Convention over configuration (opionionated)
- Better IDE integration
- Less overhead through use of repos
	- don't have to download same file 20 times because you need that file for 20 projects

Ant

- Can trace through ant files fairly easily
- quicker to learn but copy-paste intensive
- larger project size in SCM (source control), because artifacnts stored with project

### Ex. A bad Ant build.xml (but clear, legible file)

~~~
<project>
	<target name="clean">
		<delete dir="build"/>
	</target>
	
	<target name="compile">
		<mkdir dir="build/classes"/>
		<javac srcdir="src" destdir="build/classes"/>
	</target>
	
	...
</project>
~~~

### Maven pom.xml

~~~
<project xmlns="http://maven.apache.org/POM/4.0.0"
		xmlns:xsi=...
		xsi:scemaLocation=...>
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.mycompany</groupId>
	<artifactId>HelloWorld</artifactId>
	<version>0.0.1-SNAPSHOT</version>
</proejct>
~~~

Essentially, Ant is easier to learn, but it's more or less a scripting tool.
Maven, on the other hand, is centered around managing your enire project's lifecycle

## Hello Maven

Project : HelloWorld

~~~ java
HelloWorld
	|->src
		|->main
			|->java
				|->HelloWorld.java
	|
	-> pom.xml
~~~

groupId : kinda like company name
modelVersion : Version of schema structure. (XML)

~~~ java
pom.xml

<project>
	<groupId>com.pluralsight</groupId>
	<artifactId>HelloWorld</artifactId>
	<version>1.0-SNAPSHOT</version>
	<modelVersion>4.0.0.</modelVersion>
	<packaging>jar</packaging>
</project>
~~~

Now, write away java program. Though, it expects a standard file structure.

~~~ java
HelloWorld/src/main/java/HelloWorld.java

public class HelloWorld {
	public static void main(String args[]) {
		System.out.println("Hello World!");
	}
}
~~~

~~~ java
$ cd ~/HelloWorld
$ mvn clean
...
...
$ mvn compile
...
...
BUILD SUCCESS
...
...
#creates ./target/classes/HelloWorld.class
$ java ./target/classes/HelloWorld
Hello World
$ mvn package
~~~

## Maven Dependencies

Dependencies are stuff we want to use in that application.

Just list the dependency that we want, and transitive dependencies will be pulled in by Maven

Needs at a minimum 3 things:

- groupId
	- name of your company/organization... But it does not have to be that way
- artifactId
	- name of the items we want to use.
		- hibernate
		- spring core
- version

~~~ xml
<dependencies>
	<dependency>
		<groupId>commons-lang</groupId>
		<artifactId>commons-lang</artifactId>
		<version>2.1</version>
	</dependency>
</dependencies>
~~~ 

### Version

Release number of the artifact we want to use.

- Development starts off as a SNAPSHOT
	- Allows dev teams to push new codes to the repo.
	- and also Maven will look for new code at each compile (changes always downloaded)
		- ex. myapp-1.0.SNAPSHOT.jar
	- Saves you from rerelease versions for development
	- NEVER deploy to production with a SNAPSHOT
- A release, though, does not have a specific naming convention
	- ex. myapp-1.0.jar
	- ex. myapp-1.0.1.jar
- Industry common jargons
	- ex. myapp-1.0-M1.jar (milestone release)
	- ex. myapp-1.0-RC1.jar (release candidate)
	- ex. myapp-1.0-RELEASE.jar || myapp-1.0-FINAL.jar (release)

### Types

- Core packaging types
	- pom, jar, maven-plugin, ejb, war, ear, rar, par
		- jar, war, ear, rar, par are pretty much a zip file.
		- pom is referred to as a dependency pom
			- Downlaods dependencies from that pom

### Transitive Dependencies

- The MAIN reason people begin using maven
- if we add a dependency, it will download all the dependencies of that dependency.
	- will also resolve conflicts by using newer version

### Scopes

Mostly overlooked.

6 scopes available for dependencies:

- compile - defualt scope, artifact available everywhere
- provided - like compile, means that the artifact is going to be provided where it is deployed
	- servlet-api.jar
	- xml-apis
	- Available in all phases, but not included in final artifact
- runtime - not needed for compilation, but needed for execution.
- test - only available for the test compilation and execution phase
- system - DONT USE
- import - advanced topic, deals with dependencyManagement sections.

### Demo

~~~ java
pom.xml

<project>
	<groupId>com.pluralsight</groupId>
	<artifactId>HelloWorld</artifactId>
	<version>1.0-SNAPSHOT</version>
	<modelVersion>4.0.0.</modelVersion>
	<packaging>jar</packaging>
	
	<dependencies>
		<dependency>
			<groupId>common-lang</groupId>
			<artifactId>commons-lang</artifactId>
			<version>2.1</version>		
		<dependency>

		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.10</version>		
			<scope>test</scope>
		<dependency>
		
		<dependency>
			<groupId>org.hibernate</groupId>
			<artifactId>hibernate-core</artifactId>
			<version>4.1.6.Final</version>
		</dependency>
		
	</dependencies>
</project>
~~~

If you have noticed, even though you only pulled 3 dependencies, you have a ton of dependencies in your IDE due to the transitive dependency. It even solved the conflict.