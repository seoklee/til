# Spring , JPA, Hibernate

## What is Spring?

- IoC framework.
- Able to build complex Enterprise Java development
- lightweigt
- POJO based
- AOP/Proxies (Annotation oriented programming)
- Built around patterns and best practices

## JPA?

- Started as Hiberante but became standard interface.
- POJO based
    - XML configuration
    - Annotations
- ORM
- Pluggable Persistence Providers

## Why?

- Good Developers != Good DBA
- Data Model != Object model
- Configuration is better with JPA, but could be better
    + Transactions
    + Testings
    + Datasource Configuration
- Reduce code and reuduces JDBC code and more business focus. Easier to do stuff.
    - JPA removes boiler plate code
    - developers build objects, JPA bridges the gap
    - Spring handles the config
    - code can focus on testing
    - transactions are transparent to the developer.
    - Annotation based development

## Spring MVC 

- Configured in the web.xml
    - src/main/webapp/WEB-INF/web.xml
    - dispatcher servlet points to configuration file
    - src/main/webapp/WEB-INF/config/servlet-config.xml
- Controllers are annotation driven
    - @Controller
    - @ReuqestMapping

# Architecture


## MVC

C -> V

- View Selection

V -> C

- user events

V -> M

- Query

M -> V

- Report Change

C -> M

- Change

Tiered Architectures

- Separation of concerns
- reusable layers
- maintenance
    - just change one part, and you will be good instead of changing different parts.

## Components

~~~
                                 -> Repository
Controller  <---> Service ------|-> Repository
                                 -> Repository
~~~

### Controller

- Routers.
- Handles incoming requests and building the response
- Business logic should not be handled here
- Works with the service and repository tier for business logic and data gathering 


### Service

- Annotated with @Service
- The Service tier describes the verbs of a system
- where the busienss logic resides
- ensures that the business object is in a valid state
- wehre the transaction often begin (two phase commit)
- often has the same methods as the repository, but different focus

### Repository

- @Repostory
- One-to-one mapping with object
- One-to-one mapping with table

# Spring

## Setter Injection

- XML or Annotation based (@Component and @Autowire)

~~~ 
<bean name="car" class="com.pluralsight.model.Car">
    <property name="engine" ref="engine" />
</bean>

<bean name="engine" class="com.pluralsight.model.Engine">
    <property name="numofCylinders" value="6" />
</bean>
~~~

## Constructor Injection

- constructor with args in order what you want
- using XML

~~~ 
<bean name="car" class="com.pluralsight.model.Car">
    <constructor-arg index="0" ref="engine" />
</bean>

<bean name="engine" class="com.pluralsight.model.Engine">
    <property name="numofCylinders" value="6" />
</bean>
~~~

- Autowiring constructors
    - @Autowired at the constructor instead of putting it above the variable.

## Context Files

Spring Config...

- XML
- Annotation
- Java Config

Using annotations there is still some XML config

Context files are loaded multiple ways

- dispatcher servlet
- loader listener
- import (Don't use)

# Configuration

## DB

- JPA is an anstraction layer for our db
- can use almost any relational db that have dialect  for

## Persistence.xml

- if using JPA w/o Spring this is where you define settings:
    - datasource
    - allowed operations
    - caching
    - etc...
- why use Spring then:
    - can override settings per environment
    - easier to test
    - inject onto other resources
- Still need an empty persistence.xml
    - src/main/resources/META-INF/persistence.xml

~~~ xml
<persistence xmlns="http://java.sun.com/xml/ns/persistence"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/persistence http://java.sun.com/xml/ns/persistence_2_0.xsd"
    version="2.0">
    <persistence-unit name="punit">
    </persistence-unit>    
</persistence>
~~~


## web.xml

- <listener>
- <context-param>
    - contextConfigLocation
    - classpath:/jpaContext
- source/main/webapp/WEB-INF/web.xml

~~~ xml
web.xml

...
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:/jpaContext.xml</param-value>
    </context-param>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener
    </listenr>
~~~

jpaContext.xml

- used in place of the persistence.xml
- doesnt have to be named jpaContext.xml
- Loaded from the classspath
    + src/main/resources/jpaContext.xml
- Contains
    - EntityManagerFactory
        - jpa vendor
        - jpa properties
    - transaction manager
    - annotation configuration
    - datasource configuration or lookup

~~~ xml
//jpaContext.xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.2.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/beans/spring-context-3.2.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.2.xsd">

    <context:annotation-config />

    <bean class="org.springframework.orm.jpa.support.PersistenceAnnotationBeanPostProcessor"></bean>

    </beans>
~~~

### Entity Manager Factory

- LocalContainerEntityManagerFactoryBean
    - located in spring-orm.jar
    - references our persistence unit
    - injected Datasource if one isn't defined in the persistence unit
    - defines what vendor (provider) we are using
    - vendor specific JPA properties
 
~~~ xml
//jpaContext.xml
...
    <bean id="entityManagerFactory" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
        <property name="persistenceUnitName" value="punit"/>
        <property name="dataSource" ref="dataSource" />
        <property name="jpaVendorAdapter">
            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdaptor">
                <property name="showsql" value="true" />
            </bean>   
        </property>
        
        <property name="jpaPropertyMap">
            <map>
                <entry key="hibernate.dialect" value="org.hibernate.dialect.MySQL5InnoDBDialect" />
                <entry key="hibernate.hbm2ddl.auto" value="none" />
                <entry key="hibernate.format_sql" value="true" />
            </map>
        </property>
    </beans>
~~~

### Transaction Manager

-  JpaTransactionManager
    -  takes the entityManagerFactory as a ref
-  annotation driven
-  spring-tx.jar



~~~ xml
<!-- jpaContext.xml -->
...

    <bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager"> 
        <property name="entityManagerFactory" ref="entityManagerFactory"/>
    </bean>

    <tx:annotation-driven transaction-manager="transactionManager"/>
~~~

Datasouce

- for production, use connection pool and JNDI lookup

~~~ xml
<!-- jpaContext.xml -->
...

<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
<property name="driverClassName" value="com.mysql.jdbc.Driver" />
<property name="url" value="jdbc:mysql://localhost:3306/fitnessTracker?autoReconnect=true" />
<property name="username" value="root" />
<property name="password" value="password" />
</bean>
~~~


#JPA

## Why JPA?

- interface for a specific implementation of ORM

## What is JPA?

- Actually just a spec
- Thought of as an ORM tool (ORM)
    - an ORM is an implementation of JPA
- A spec for accessing, persisting, and managing data b/w java object / classes and a relational database
- NOT SQL

## Hiberante History

- Hibernate directly or JPA using Hibernate
    - Another abstraction. decoupling

## Entity

- POJO
- Entity
    - @Entity
- ID
    - @Id

## JPA / Hibernate Configuration 

- LocalContainerEntityManagerFactoryBean
    - jpaPropertyMap
- hibernate.dialect
- hibernate.format_sql
- hibernate.hbm2ddl.auto
    - create
    - create-drop
    - update
    - validate
    - none

~~~
    <property name="jpaPropertyMap">
        <map>
            <entry key="hibernate.dialect" value="org.hibernate.dialect.MySQL5InnoDBDialect" />
            <entry key="hibernate.hbm2ddl.auto" value="create" />
            <entry key="hibernate.format_sql" value="true" />
        </map>
    </property>
~~~


~~~ java

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
public class Goal {

    @Id
    @GeneratedValue
    private Long id;

    @Range(min = 1, max = 120)
    private int minutes;

    //getters & setters
}
~~~

# JPA Annotation

 - @Entity declares the object as an ENtity and now the DB will be aware of it
 - @Table - descrives more specific details about the DB, ie:name, schema
 - @Id - identifier attribute for a simple primary key type
 - @GeneratedValue = used in conjuction with @id
     - IDENTITY - used to specify a db identity column
     - AUTO - automatically chooses an implementation based off of the underlying database
     - SEQUENCE - works with a dequence (if the db supports them), see @SequenceGenerator
     - Table - specifies that a db will use an identity table and column to ensure uniqueness, see @TableGeneartor


~~~ java

@Entity
@Table(name="goals")
public class Goal {
    ...
}
~~~

## default columns

- dont have to accept the default that are created for us
- @Column will allow us to override column names or add descriptive info about it
    - columnDefinition
    - insertable
    - length
    - name
    - nullable
    - precision
    - scale
    - table
    - unique
    - updatable

~~~ java
public class Goal {

    @Id
    @GeneratedValue
    @Column(name="GOAL_ID")
    private Long id;

    @Raange(min = 1, max =120)
    @Column(name="MINUTES")
    private int minutes;
     
}
~~~

- hbm2dll.auto, create-drop drops the only the table not the schema.

## HOWTO USE

- PersistenceContext
    - injtect the EntityManager in our code
- @Service
    - Spring service where business logic is located
- @Repository
    - Spring DAO object, db interaction ocdcurs
- @Transactional
    - used to start a transaction

## Service

~~~ java
//GoalService.java

package com.pluralsight.service;

public interface GoalService {
    Goal save(Goal goal);
}

//GoalServiceImpl.java

@Service("goalService")
public class GoalServiceImpl implements GoalService {
    public Goal save(Goal goal) {
        return null;
    }
}

//GoalController

@Controller
@SessionAttributes("goal")
public class GoalController {

    @Autowired
    Private GoalService goalService;


    ...
    @RequestMapping(value = "addGoal", method = RequestMethod.POST)
    public String updateGoal(@Valid @ModelAttribute("goal") Goal goal, BindingResult result) {
        ...
    }
}
~~~

## Repository

~~~ java
//GoalRepository.java
package com.pluralsight.repository;

public interface GoalRepository {
    Goal save(Goal goal);
}

//GoalRepositoryImpl.java

public class GoalRepositoryImpl implements GoalRepository {

    @PersistenceContext
    private EntityManager em;

    public Goal save(Goal goal) {
        em.persist(goal);
        em.flush();
        return goal;
    }
}

//GoalServiceImpl.java

@Service("goalService")
public class GoalServiceImpl implements GoalService {

    @Autowired
    private GoalRepository goalRepository goalRepository;

    public Goal save(Goal goal) {
        goalRepository.save(goal);
    }
}
~~~

~~~
//jpaContext.xml
...
<context:annotation-config />
<context:component-scan base-package="com.pluralsight.controller"/>
...

//servlet-config.xml
...
<context:component-scan base-package="com.pluralsight.controller"/>
...
~~~

## Join Types

- @OneToOne
- @OneToMany
- @ManyToOne
- @ManyToMany

### @OneToMany

- one-to-many relationship b/w an object and a list of objects

~~~ java

@OneToMany(mappedBy="goal", cascade=CascadeType.ALL)
private List<Exerciese> exercises = new ArrayList<>();

~~~

~~~ java
//Exercise.java
@Entity
public class Exercise {

    @Id
    @GenerateValue
    private Long id;

    @ManyToOne
    private Goal goal;

    //int minutes and string activity 

    //setter and getters
}

// Goal.java
@Entity
@Table(name="goals") 
public class Goal {
    ...

    @OneToMany(mappedBy="goal", cascade=CascadeType.ALL)
    private List<Exercise> exercises = new ArrayList<>();

    //getter and setters
}
~~~

### ManyToOne

~~~ java
//MinutesController.java
@RequestMapping(value = "/addMinutes", method = RequestMethod.POST) 
public String addMinutes(@Valid @ModelAttribute ("exercse") Exercise exercise, HttpSession session, BindingResult result) {

    if(result.hasErrors()) {
        return "addMinutes";
    } else {
        Goal goal = (Goal)session.getAttributes("goal");

        exercise.setGoal(goal);
        exerciseService.save(exercise);
    }
}

//ExerciseService.java
public interface ExerciseService {

    Exercise save (Exercise ex);
}

//ExerciseServiceImpl.java
public class ExerciseServiceImpl implements ExerciseService {
    public Exercise save(Exercise exercise) {
        ... 
    }
}

//ExerciseRepository.java

public interface ExerciseRepository {
    Exercise save (Exercise ex) {

    }
}

//ExerciseRepositoryImpl.java
@Repository("exerciseRepository")
public interface ExerciseRepositoryImpl implements ExerciseRepository {

    @PersistenceContext
    private EntityManager em;

    Exercise save (Exercise ex) {
        em.persist(ex);
        em.flush();
        return ex;
    }
}
~~~

~~~ java

//ExerciseServiceImpl.java
public class ExerciseServiceImpl implements ExerciseService {

    @Autowired
    private ExerciseRepository exerciseRepository;

    @Transactional
    public Exercise save(Exercise exercise) {
        exercise = exerciseRepository.save(exercise);
        return exercise;
    }
}

~~~