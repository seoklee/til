# Starting Spring Boot

Sample project - WWII Shipwreck Locator

#### Spring Boot Dependency Management

- BOM - Bill of Material 
    - Collections of frameworks and dependency for Spring Boot
    - Parent tag in POM file brought this in alongside all the project directory hierarchy.
        - That's why you dont have to supply a version number in springframework.boot

#### Other Spring Initializer

- start.spring.io
- Spring boot CLI 
- spring-projects/spring-boot github

#### How does Spring Boot Work?

- Java
    - Main method entry point
- Spring Application
    - Spring Context
    - Spring environment
    - Initializer
- Embedded Server
    - Default is Tomcat
    - Auto configured

--- 

public static void main( ... )

- starts java and then the application

---

@SpringBootApplication

- A convenience annotation that wraps commonly used annotations with Spring Boot

---

@Configuration, 
    
- Spring configuration on startup. Using this instead of XML

@EnableAutoConfiguration, 

- Auto configures frameworks. If other Spring Boot enabled frameworks is detected, auto-configure it

@ComponentScan

- Scans project for Spring components. Look for components such as repository, controller, and services. Be aware of the location of App java.

---

SpringApplication.run(...)

- Starts Spring, creates spring context, applies annotations, and start, and sets up the code

---

#### Why Move to Containerless Deployment?

Container Deployment

- pre-setup and configuration 
- need to use files like web.xml to tell container how to work
- Environment configuration is external to your application

Application Deployments

- Runs anywhere Java is setup (think cloud deployments)
- Container is embedded and the app directs how the container works.
- Environment configuration is interal to your application

---

Spring MVC provides REST APIs and Clients connects via HTTP/HTTPS

Default static conetent locations

- classpath
    - /static
    - /public
    - /resources

JS angular files added.

---

#### Spring MVC Integration Overview

spring-boot-starter-web in pom.mxl does...

Sets up ViewResolvers

Sets up static resource serving

Sets up HttpMessageConverter

Sets up cutomizable hooks

---

#### Properties and Environmental Configuration

applicaiton.properties

- place on classpath root
- yaml or Properties format

Environmental configuration

- application-{profile}.properties
    - i.e. application-dev.properties

First apply properties at application.properties, then overwrites based on the profiles accordingly.

---

#### Configuring/Accessing Data Source

- JPA
- Spring Data JPA
    - Only needs to add Spring Data JPA on the pom file.

- FlywayDB Migration (For DB Migration)

