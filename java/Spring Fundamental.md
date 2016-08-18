#Spring Fundamental

---

## What is Spring?

- framework originally built to reduce complexity of Jave EE developement
	- very weightlight and unobstrusive compared to older J2EE
- or without EJB (Enterprise Java Bean)
- allows enterprise devleopement without application server
- POJO based and interface driven
- AOP/Proxies
- built around patterns and best practices
	- template method
	- annotation based configuration 

## The problem

- Testability
- Maintainability
- Scalability

### Solution

- Removes configuration/lookup code
- developers can focus on the business needs
- code can focus on testing
- annotation or xml based development

### How does it work?

- Everything is simple POJO
- Essentially a glorified HashMap
- Can be used as a Registry

---

## Architecture

### Sample App

~~~ java
//Customer.java

package com.pluralsight.model

public class Customer {
    private String lastname;
    private String firstname;
    //getter and setter
}
// CustomerRepositoryImpl.java
public interface CustomerRepositoryImpl {
    List<Customer> findAll();
}

//HibernateCustomerRepositoryImpl
package com.pluralsight.repository;
import com.pluralsight.model.Customer;

public class HibernateCustomerRepositoryImpl {

    public List<Customer> findAll() {

        List<Customer> customers = new ArrayList<>();
        Customer customer = new Customer();

        customer.setFirstName("Seok");
        customer.setLastName("Hansen");

        customers.add(customer);

        return customers.
    }
}

//CustomerService.java
public interface CustomerService {
    List<Customer> findAll();
}

//CustomerServiceImpl
package com.pluralsight.service;

public class CustomerServiceImpl implements CustomerService {

    private CustomerRepository customerRepository = new HibernateCustomerRepositoryImple();

    public List<Customer> findAll() {
        return customerRepository.findAll(); 
    }
}

//application 
public class Application {
    public static void main(String args) {
        CustomerService service = new CustomerServiceImpl();
        System.out.println(service.findAll());
    }
}
~~~

### Configuration

Get rid of instantiating new objects. (w/o new operator)

applicationContext.xml

There are namespaces that aid in configuration and validation

~~~
//src/applicationContext.xml

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    csi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

</bean>
~~~

### Xml Configuration

- Beans
    - essentially classes
    - defining beans replaces using the keyword "new"
    - define the class, use the interface?
- Consturctor Args
    - Used to reference properties of the consturctor
- Properties
    - Getters and Setters of the POJO that we are working with
- References
    - References to other beans that we have defined
- Values
    - basic primitive values that we are setting on our POJO

### Beans

- Id or Name
    - one or the other, can be used interchangeably
    - Id has to be a vaild XML identifier
        - can't contain special characters, ie:"*", "/", "."
    - Name can contain special characters
        - often doesn't matter with just Spring, but when building URLs with Spring MVC can be problematic
- Default No-Args Consturctor
    - setter injection vs Constructor Injection
- Class

~~~ 
<bean name="customerRepository"
    class="com.pluralsight.repository.HibernateCustomerRepositoryImpl">
</bean>
~~~



~~~ java
//CustomerServiceImpl
package com.pluralsight.service;

public class CustomerServiceImpl implements CustomerService {

    // no instantiating!
    private CustomerRepository customerRepository;

    //getter and setter of customerRepository
}

public class Application {

    public static void main(String[] args) {

        ApplicationContext appContext = new ClassPathXmlApplicationContext("applicationContext.xml");

        CustomerService customerService = appContext.getBean("customerService", CustomerService.class);        
    }
}

~~~

~~~ xml
// - src/applicationContext.xml

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    csi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean name="customerRepository" class="com.pluralsight.repository.HibernateCustomerRepositoryImpl"/>

    <bean name="customerService" class="com.pluralsight.service.CustomerServiceImpl">
            <property> name="customerRepository" ref="customerRepository"></property>
    </bean>
</bean>
~~~

### Setter vs Constructor Injection

- Setter injection is more common
    - you have the setter more often, but dont have custom constructor most of the time

Setter Injection

~~~ xml
    <bean name="customerService" class="com.pluralsight.service.CustomerServiceImpl">
            <property> name="customerRepository" ref="customerRepository"></property>
    </bean>
~~~

Constructor injection

~~~ xml
    <bean name="customerService" class="com.pluralsight.service.CustomerServiceImpl">
        <constructor-arg index="0" ref="customerRepository"/>
    </bean>
~~~

~~~
//CustomerService.java
public class CustomerServiceImpl implements CustomerService {

    // no instantiating!
    private CustomerRepository customerRepository;

    public CustomerServiceImpl(CustomerRepository customerRepository) {
        this.customerRepository = customerRepository
    }

    //getter and setter of customerRepository
}

~~~

### Autowiring

- Spring can automatically wire beans together for you
- by Type
    - allows a property to be autowired if exactly one bean of the property type exists in the conatainer. if more than one exists, a fatal exception is thrown. If no matching beans, nothing happens
- byName
    - Autowiring by property name.
- consturctor
    - analogous to byType, but applies to constructor arguments. If there is not exactly one bean of the constructor argument type in the container, fatal error
- no

~~~ xml
// - src/applicationContext.xml

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    csi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean name="customerRepository" class="com.pluralsight.repository.HibernateCustomerRepositoryImpl"/>

    <bean name="customerService" class="com.pluralsight.service.CustomerServiceImpl" autowire="constructor">
        <constructor-arg index="0" ref="customerRepository"/>
    </bean>

    <!-- or -->
    <!-- <bean name="customerService" class="com.pluralsight.service.CustomerServiceImpl" autowire="byType">
        <constructor-arg index="0" ref="customerRepository"/>
    </bean> -->

</bean>
~~~

---

## Annotation Configuration

### applicationContext.xml

- just like XML config, we need a way to bootstrap the annotation scanner
- uses the same conventions as the XML configuration

### Component Scanner

- part of the context namespace

~~~ xml
 xmlns:context="http://www.springframework.org/schema/context"

 http://www.springframework.org/schema/context
 http://www.springframework.org/schema/context/spring-context-3.2.xsd"
~~~

- Two elements needed to configure

~~~ xml
    <context:annotation-config/>
    <context:component-scan base-package="com.pluralsight" />
~~~

### sample applicationContext

#### new project

~~~ xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/beans"
    xcsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <context:annotation-config/>

    <context:component-scan base-package="com.pluralsight"/>
</beans>
~~~

### Stereotype Annoataions

- @Component, @Service, @Repository
- Sementically the Same
    - @Service and @Repository both extend @Component, but don't add any features
- @Component
    - Regular Components/beans, any POJO
- @Service
    - business logic
- @Repository
    - Data Access tier / DB intersaction layer

~~~ java
//HibernateCustomerRepositoryImpl
package com.pluralsight.repository;
import com.pluralsight.model.Customer;

@Repository("customerRepository")
public class HibernateCustomerRepositoryImpl {

    public List<Customer> findAll() {

        List<Customer> customers = new ArrayList<>();
        Customer customer = new Customer();

        customer.setFirstName("Seok");
        customer.setLastName("Hansen");

        customers.add(customer);

        return customers.
    }
}

//CustomerServiceImpl
package com.pluralsight.service;

@Service("customerService")
public class CustomerServiceImpl implements CustomerService {

    private CustomerRepository customerRepository = new HibernateCustomerRepositoryImple();

    public List<Customer> findAll() {
        return customerRepository.findAll(); 
    }
}
~~~

### Autowired

- using annotation is more straightforward
- autowiring method is somewhat hidden because it is tied to where you place the annotation
- can be placed...
    - Member variables
        - Use reflection
    - Constructor
    - Setter
        - use setter

~~~ java

//CustomerServiceImpl
package com.pluralsight.service;

@Service("customerService")
public class CustomerServiceImpl implements CustomerService {

    //member variables
    @Autowired
    private CustomerRepository customerRepository

    public List<Customer> findAll() {
        return customerRepository.findAll(); 
    }

    //OR setter injection
    //@Autowired
    //void setCustomerRepository(CustomerRepository customerRepository) {
    //    this.customerRepository = custumRepository
    //} 

    //OR consturctor injection
    //@Autowired
    public CustomerServiceImpl(CustomerRepository customerRepository) {
        this.customerRepository = customerRepository;
    }

}
~~~


### Summery

- applicationContext.xml
- component scanner
- stereotype annotation
- autowired

---

## Spring config using Java

Separating config with source code.

### No applicationContext.xml

- effort reduce XML
- thus Java Config
- most features of Spring can be configured using Java Configuration

@Configuration

- class w/ @XConfiguration annotation replace appContext files
- used in conjunction with thbe @Bean annotation to get instances of Spring beans
- @configuration class level annotation
- @bean is a method level annotation
- class and method names can be anything.

##### New Project

~~~ java
//AppConfig.java
@Configuration
public class AppConfig {

    @Bean(name = "customerService")
    public CustomerService getCustomerService() {
        return new CustomerServiceImpl();
    }
}
~~~

### Setter Injection

- simple as a method call
- simply calling a setter

~~~ java
//AppConfig.java
@Configuration
public class AppConfig {

    @Bean(name = "customerService")
    public CustomerService getCustomerService() {
        return new CustomerServiceImpl();
    }

    @Bean(name="custmerService")
    public CustomerService getCustomerService() {
        CustomerServiceImpl customerService = new CustomerServiceImpl();
        customerService.setCustomerRepository(getCustomerRepository());
        return customerService;
    }

    @Bean(name="customerRepository") 
    public CustomerRepository getCustomerRepository() {
     return new HibernateCustomerRepositoryImpl();
    }
}

//CustomerServiceImpl.java
public class CustomerServiceImpl implements CustomerService {

    private CustomerRepository customerRepository;

    public void setCustomerRepository(CustomerRepository customerRepository) {
        this.customerRepository = customerRepository;
    }

    public List<Customer> findAll() {
        return customerRepository.findAll(); 
    }
}

public class Application {

    public static void main(String[] args) {

        ApplicationContext appContext = new AnnotationConfigApplicationContext(AppConfig.class);

        CustomerService service = appContext.getBean("customerService", CustomerService.class);

        System.out.println(service.findAll().get(0).getFirstname());
    }
}
~~~

### Consturction injection

Almost same, but now use constructors.

~~~ java
//AppConfig.java
@Configuration
public class AppConfig {

    ...

    @Bean(name="custmerService")
    public CustomerService getCustomerService() {
        CustomerServiceImpl customerService = new CustomerServiceImpl(getCustomerRepository);
        return customerService;
    }

    ...
}

//CustomerServiceImpl.java
public class CustomerServiceImpl implements CustomerService {
    ...

    public CustomerServiceImpl() {

    }

    public CustomerServiceImpl(CustomerRepository customerRepository) {
        this.customerRepository = customerRepository;
    }

    ...

}
~~~


### Autowired

Add @ComponentScan annotation:

~~~ java
@ComponentScan({"com.pluralsight"})
~~~

- just like XML config, mark whatever you want as @Autowired
    - byName uses the @Bewan name
    - byType uses the instance type

~~~ java
//AppConfig.java
@Configuration
@ComponentScan({"con.pluralsight"})
public class AppConfig {

    ...

    @Bean(name="custmerService")
    public CustomerService getCustomerService() {
        CustomerServiceImpl customerService = new CustomerServiceImpl();
        return customerService;
    }

    //@Bean(name="customerRepository") 
    //public CustomerRepository getCustomerRepository() {
    //return new HibernateCustomerRepositoryImpl();
    //}

    ...
}


//CustomerServiceImpl.java
public class CustomerServiceImpl implements CustomerService {

    @Autowired
    private CustomerRepository customerRepository;

    public void setCustomerRepository(CustomerRepository customerRepository) {
        this.customerRepository = customerRepository;
    }

    @Bean(name = "customerService")
    public CustomerService getCustomerService() {
        return new CustomerServiceImpl();
    }

    public List<Customer> findAll() {
        return customerRepository.findAll(); 
    }
}

//HibernateCustomerRepoImpl
@Repository("customerRepository")
public class HibernateCustomerRepositoryImpl implements CustomerRepository {
...
}
~~~

--- 

## Bean Scope

### Scopes

- 5 scopes in all
- valid in any configuration
    - singleton - (default)
    - prototype
- valid only in web-aware Spring projects
    - request
    - session
    - global

### Singleton 

- default bean scope
- one instance per Spring container/context (or generally one / JVM... kinda)
- Java Config

~~~ java
@Bean(name = "customerService")
@Scope("singleton")
public CustomerService getCustomerService() {

}
~~~

- java config needs the AOP jar added to the classpath
- XML config (no need AOP)

~~~ xml
<bean name="customerService"
    class="com.pluralsight.service.CustomerServiceImpl"
    autowire="byName" scope="singleton">
~~~

### Prototype

- A new instance per request
- useful in guaranteeing that you want a new/unique instance per request

~~~ java
Bean(name = "customerService")
@Scope("prototype")
public CustomerService getCustomerService() {

}
~~~

~~~ xml
<bean name="customerService"
    class="com.pluralsight.service.CustomerServiceImpl"
    autowire="byName" scope="prototype">
~~~

### Web Scopes

- Request
    - returns a single bean per HTTP request
- Session
    - Returns a single bean per HTTP session
- Global session
    - Single bean per app

--- 

## Reading Properties files in Spring

### XML Config

- Context namespace has utilities to help


~~~ xml
<!-- be sure that context is included in the xml -->

    <context:property-placeholder location="app.properties">
~~~


~~~ xml
<bean name-"customerRepository" class="com.pluralsight.repository.HibernateCustomerRepositoryImpl">
    <property name="someValue" value="${someProperty}" />
</bean>
~~~

- Can be injected into XML:

~~~
// app.properties
someProperty=Foo
~~~

~~~ xml
<!-- //appContext.xml -->
    <context:property-placeholder location="app.properties">
~~~

~~~ java
//HibernateCustomerRepositoryImple.java

@Repository("customerRepository")
public class HibernateCustomerRepositoryImpl implements CustomerRepository {
...
    @Value("${someProperty}")
    private String someValue;
}
~~~

--- 

- Also injected as an annotation:

~~~ java
@Value("${someProperty}")
private String someValue;
~~~

Java Config

- annotation used to load properties files:

~~~ java
    @PropertySource("app.properties")
~~~

- To use the value annotation in your POJos, need to configure a bean

~~~ java
@Bean
public static PropertySourcesPlaceholderConfigurer getPropertySourcesPlaceholderConfiguerer) {
    return new PropertySourcesPlaceholderConfigurer();
}
~~~

- it can now be injected
z
~~~ java
@Value("${someProperty}")
private String someValue;
~~~

---

~~~ java
//AppConfig.java

@Configuration
@ComponentScan({"com.pluralsight"})
@PropertySource("app.properties") //com/pluralsight/model.app.properties

public class AppConfig {
    @Bean
    public static PropertySourcesPlaceholderConfigurer getPropertySourcesPlaceholderConfiguerer) {
        return new PropertySourcesPlaceholderConfigurer();
    }
}

~~~


