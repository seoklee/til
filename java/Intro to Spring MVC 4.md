# Intro to Spring MVC

## Overview

### Java Web Application Dev

- Struts
- tapestry 5
- wicket
- Google Web Toolkit
- JSF from Oracle (J2EE-based)
- Seams (Jboss)
- Stripes (light-weight)
- Spring MVC

### What is Spring MVC?

- A Web framework built around the principles of Spring
- POJO based and Interface Driven
- Based on a Dispatcher Servlet / Front Controller pattern
    - MVC stands for Model-View-Controller
- Very lightweight and unobtrusive compared to other framework
- Built from the shortcoming of Struts 1
- Support for : 
    - Themes
    Locales/i18n
    - JSP based
    - REST based
    - ... etc
    - Annotation based config
    - Spring Services/Beans

### Architecture

- Stack looks like...

~~~
Custom App
SPring MVC / Spring
Java Servlets/JEE
~~~

### Request / Response Lifecycle

~~~
imcoming request ->        -> Delegates Request          -> Handle Request
                 front Controller                 Controller           Backend
<--- response     |  |       <-- Delegate Rendering        <-   Create Model
                  |  |model

            View Template  (Render Response)
~~~

### Vocabulary

- DispatcherServlet - The entry / configuration point for the app
- Controller - command pattern object that handles the request and determines which view to route to
- RequestMapping - the URL and request type that a method is tied to.
- ViewResolver - Used to locate JSP pages or whatever view we are using
- Servlet-config - config file per DispatcherServlet
- POJO - plain old java object

### Summery

- J2EE Based
- stable release cycle
- backward compatible
- active community
- best practice patterns
- lightweight and unobstrusive
- flexible to use with WEB services, javascript UI, standard HTML


## Building

org.apache.mavebn.archetype 
maven-archetype-webapp

### Maven Config

Depedencies
    - dep
        + groupId org.springframeowrk
        + artifactId spring-webmvc
        + version 3.2.0.RELEASE
    + dep
        + groupId javax.servlet
        + artifactId servlet-api
        + version 2.5
        + scope provided
    + dep
        + groupId javax.servlet
        + artifactId jstl
        + version 1.2
        + scope provided

~~~ xml
//pom.xml

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.pluralsight</groupId>
  <artifactId>FitnessTracker</artifactId>
  <packaging>war</packaging>
  <version>0.0.1-SNAPSHOT</version>
  <name>FitnessTracker Maven Webapp</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>3.2.0.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>jstl</artifactId>
        <version>1.2</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>2.5</version>
        <scope>provided</scope>
    </dependency>
  </dependencies>
  <build>
    <finalName>FitnessTracker</finalName>
  </build>
</project>

~~~

### Spring MVC Config

- configure web.xml
- configure servlet-config.xml
- add controller
- add view

### Tomcat

Web container. War file top of the container.

### Views

put it into WEB-INF/jsps/...

Why?

- Security, does not know what the file is...
- control user experience

Need to use InternalResourceViewResolver. When I ask for JSP page, look it into this.

### Controller

- Annotation based
    - @Controller
- Typically named around business domain
- Path set using annotations
    - @RequestMapping

### schema in the web.xml

~~~ xml
// WEB-INF/web.xml

<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">

    <servlet>
        <servlet-name>fitTrackerServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/config/servlet-config.xml</param-value>
        </init-param>
    </servlet>

    <servlet-mapping>
        <servlet-name>fitTrackerServlet</servlet-name>
        <url-patterns>*.html</url-patterns>
    </servlet-mapping>
</web-app>
~~~

### servlet-config.xml

~~~ xml
// WEB-INF/config/servlet-config
?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.2.xsd">

        <mvc:annotation-driven/>
        <context:component-scan base-package="com.pluralsight.controller"/>

</beans> 
~~~


### Creating controller

~~~ java
//src/main/java/controller/HelloController.java

package com.pluarlsight.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;

@Controller
public class HelloController {
    @RequestMapping(value ="/greeting")
    public String sayHello(Model model) {
        model.addAttribute("greeting", "Hello World");
        return "hello";
    }
}
~~~

~~~ jsp
//src/main/webapp/WEB-INF/jsp/hello.jsp

<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
<h1>${greeting}</h1>
</body>
</html>
~~~

##### Long-hand way

~~~ xml
// WEB-INF/config/servlet-config
?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.2.xsd">

        <mvc:annotation-driven/>
        <context:component-scan base-package="com.pluralsight.controller"/>

        <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
            <property name="prefix" value="/WEB-INF/jsp/"/>
            <property name="suffix" value=".jsp"/>
        </bean>
</beans> 
~~~

##### short way

~~~ xml
// WEB-INF/config/servlet-config
?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.2.xsd">

        <mvc:annotation-driven/>
        <context:component-scan base-package="com.pluralsight.controller"/>

        <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" 
        p:prefix="/WEB-INF/jsp/" p:suffix=".jsp" />
</beans> 
~~~

## Architecture

MVC

### Application Layer

View                (JSPs)
Controller          (Controller, @Controller)
Data Model / DB     (Model Object)

### Components

~~~
                          |-> Repo
Controller <---> Service  --> Repo
                          |-> Repo
~~~

### Controller

- handles incoming requests and building response.
- business logic should NOT be handled here 
- Handles exceptions and routes the view accordingly

### Service

- @Service
- describves the verbs (actions) of a system
- where the business logic resides
- ensures that the business object is in a valid state
- where transaction often begin (two phase commit)
- often has the same methods as the repo, but different focus
    - what if user not found, what state of object should be returned is in service
    - repo just gets it from db

### Repository

- Annotated with @Repository
- The repository tier describes the nounds (data) of a system
- Focused on persisting and interactig with the db
- one-to-one mapping with an object
- often a one-to-one mapping with a db table

## Controllers

Heart and gateway of Spring MVC

### Responsibilities

- Interpret user input and transform to input to a model
- provide access to business logic
- determines view based off of logic
- interprets Excpetions from the business logic / service tier

### @Controller

~~~ java
@Controller
public class MinutesController {

    @RequestMapping(value ="/addMinutes")
    public String addMinutes(Model model) {
        return "addMinutes";
    }
}
~~~

~~~ jsp
//addMinutes.jsp
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1"%>    
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Add Minutes Page</title>
</head>
<body>
<h1>Add Minutes Exercised</h1>
<form:form>
    <table>
        <tr>
            <td>Minutes Exercise For Today:</td>
            <td><form:input path="minutes"/></td>
        </tr>
        <tr>
            <td colspan="2">
                <input type="submit" value="Enter Exercise"/>
            </td>   
        </tr>
    </table>
</form:form>
</body>
</html>

<!-- 'path' will bind to an object --->
~~~

web.xml adds dispatcherServlet and maps it to WEB-INF/config/servlet-config.xml

servlet-config finds all the controllers and views with given information

### Passing Params

Passing data from JSP page to controller, use tags library

### @ModelAttribute

Retrieving data at/from Controller

- Used with an HTTP GET
- Used with an HTTP Post
- Works with POJOs
- Can be validated with Binding Results

~~~ java
//src.main.java.com.pluralsight.model.Exercise.java
package com.pluralsight.model;

public class Exercise {

    private int minutes;

    public int getMinutes() {
        return minutes;
    }

    public void setMinutes(int minutes) {
        this.minutes = minutes;
    }
    
}
~~~

~~~ java
@Controller
public class MinutesController {

    @RequestMapping(value ="/addMinutes")
    public String addMinutes(@ModelAttributes ("exercise") Exercise exercise) {
        return "addMinutes";
    }
}
~~~

~~~ jsp
//addMinutes.jsp
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1"%>    
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Add Minutes Page</title>
</head>
<body>
<h1>Add Minutes Exercised</h1>
<form:form commandName="exercise">
    <table>
        <tr>
            <td>Minutes Exercise For Today:</td>
            <td><form:input path="minutes"/></td>
        </tr>
        <tr>
            <td colspan="2">
                <input type="submit" value="Enter Exercise"/>
            </td>   
        </tr>
    </table>
</form:form>
</body>
</html>

<!-- 'path' will bind to an object --->
~~~


## Views

Place it under WEB-INF/jsp/ so it can't be deeplinked. Also gives more control user experience.

Need to use InternalViewResolvers to find page in subdirectories .

### Resolving a view

Returning a page with string that has the JSP page naem.

You can have multiple view resolvers.

#### Chaining

~~~ java
@Controller
public class MinutesController {

    @RequestMapping(value ="/addMinutes")
    public String addMinutes(Model model) {
        System.out.println("exercise : " + exercise.getMinutes());
        return "forward:addMinutes";
    }

    @RequestMapping(value ="/addMoreMinutes")
    public String addMinutes(Model model) {
        System.out.println("exercising : " + exercise.getMinutes());
        return "addMinutes";
    }
}
~~~

Break. Why? Fix it... like below

~~~ java
@Controller
public class MinutesController {

    @RequestMapping(value ="/addMinutes")
    public String addMinutes(Model model) {
        return "forward:addMinutes.html";
    }
}

~~~

~~~
Output
exercise : 16
exercise : 16
~~~

By doing this, you bypass the viewresolver first time, went outside the framework. And went to the other method again

By changing forward to redirect...


~~~ java
@Controller
public class MinutesController {


    @RequestMapping(value ="/addMinutes")
    public String addMinutes(Model model) {
        return "redirect:addMinutes.html";
    }
}

~~~

~~~
Output
exercise : 16
exercise : 0
~~~

went outside of the framework and made a new reqeust.

### Resolving Static Files

~~~ java
@Controller
public class MinutesController {

    @RequestMapping(value ="/addMinutes")
    public String addMinutes(Model model) {
        System.out.println("exercise : " + exercise.getMinutes());
        return "addMinutes";
    }
}
~~~

~~~ xml
// WEB-INF/config/servlet-config
?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.2.xsd">

        <mvc:annotation-driven/>
        <context:component-scan base-package="com.pluralsight.controller"/>

        <mvc:resources location="pdfs" mapping="/pdfs/**"/>

        <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" 
        p:prefix="/WEB-INF/jsp/" p:suffix=".jsp" p:order="1"/>

        <bean class="org.springframework.web.servlet.view.BeanNameViewResolver" p:order="0"/>
</beans> 
~~~


~~~ xml
// WEB-INF/web.xml

<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">

    <servlet>
        <servlet-name>fitTrackerServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/config/servlet-config.xml</param-value>
        </init-param>
    </servlet>

    <servlet-mapping
        <servlet-name>fitTrackerServlet</servlet-name>
        <url-patterns>*.html</url-patterns>
    </servlet-mapping>

    <servlet-mapping>
        <servlet-name>fitTrackerServlet</servlet-name>
        <url-patterns>/pdfs/**</url-patterns>
    </servlet-mapping>
</web-app>
~~~

### Summery

- Different View Technologies
- JSP locations
- View Resolvers
- Static Views


