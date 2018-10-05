# Spring Boot in Action

## Bootstarting Spring

- Spring Boot offers a new paradigm for developing Spring applications with minimal friction. 
- With Spring Boot, we will be able to develop Spring applications with more agility and 
  be able to focus on addressing our application's functionality needs with minimal(or possibly no) thought of configuring Spring itself.
  In fact, one of the main things that Spring Boot does is to get Spring out of our way so we can get stuff done.

## Spring Rebooted

- Spring started as a lightweight alternative to JEE/J2EE/EJBs.
- It offered a simpler approach to enterprise Java development, utilizing dependency injection + aspect oriented programming.
- It achieves the above using Plain Old Java Objects(POJOs).
- Spring because it was lightweight in terms of component code, was heavyweight in terms of configuration.
- Initially configured using XML, then Spring 2.5 onwards annotation-based component scanning was allowed.
- Spring 3.0 introduced a Java-based configuration as a type-safe and refactorable alternative to XML.
- Still enabling certain Spring features such as transaction management and Spring MVC required explicit configuration in web.xml or spring initializer.
- All of this configuration represents `Developer Friction`. Any time spent writing configuration is time spent not writing application logic.
- The mental shift required to think about configuring a Spring feature distracts from solving the business problem. 
  Like any framework, Spring does a lot for us, but it demands that we do a lot for it in return.
- Dependency management along with managing the versions of libraries that work well together represents another form of friction. (as we are not writing application code during this time)

## Enter Spring Boot!!! Taking a fresh look at Spring.

For developing a very simple Hello World web application with Spring we need to have the below:

1. A project structure complete with Maven/Gradle build file including required dependencies.(At least Spring MVC + Servlet API are needed as dependencies)
2. A `web.xml` file or `WebApplicationInitializer` implementation that declares Spring's DispatcherServlet.
3. A Spring configuration that enables Spring MVC.
4. A controller class that will respond to HTTP requests with "Hello World".
5. A web application server such as Tomcat to deploy the application to.

Out of the above list only `one` item is specific to developing the Hello World functionality: the controller. 
The rest is generic boilerplate needed to develop any web application with Spring.

```groovy

    @RestController
    class HelloController {
        
        @RequestMapping("/")
        def hello(){
            return "Hello World!";
        }
        
    }


```

In the above code we have no configuration, no web.xml, no build specification, no application server.
Still it is the entire application with Spring Boot taking care of the logistics of executing the application.

If we have the Spring Boot CLI installed we can run the above code using:

    > spring run HelloController.groovy

## Spring Boot Essentials

1. Automatic Configuration - Spring Boot can automatically provide configuration for application functionality common to many Spring applications.
2. Starter Dependencies - We tell Spring Boot what kind of functionality we need, and it will ensure that the libraries needed are added to the build.
3. The command-line interface - This optional feature of Spring Boot lets us write complete applications with just application code,
   but no need for a traditional project build.
4. The Actuator - Gives us insight into what's going on inside of a running Spring Boot application.

### Auto-Configuration

If we want to set up a Spring JdbcTemplate with an H2(in-memory embedded database) datasource we will need to write the code as below:

```java

    @Bean
    public JdbcTemplate jdbcTemplate(DataSource dataSource) {
        return new JdbcTemplate(dataSource);
    }

```
The above bean declaration creates an instance of JdbcTemplate, injecting it with its one dependency, a DataSource(which is configured below).

```java

    @Bean
    public DataSource dataSource() {
        return new EmbeddedDatabaseBuilder()
            .setType(EmbeddedDatabaseType.H2)
            .addScripts('schema.sql', 'data.sql')
            .build();
    }

```

This bean configuration method creates an embedded database, specifying two SQL scripts to execute on the embedded database.
The build() method returns a data-source that references the embedded database.

Neither of these two bean configuration methods is terribly complex or lengthy. But they represent just a fraction of the configuration in a typical Spring application.
Moreover, there are countless Spring applications that will have these exact same methods.
Any application that needs an embedded database and a JdbcTemplate will need those methods.
In short, it's boilerplate configuration.

Spring Boot can automatically configure these common configuration scenarios. If Spring Boot detects that we have the H2 database library in our application's classpath,
it will automatically configure an embedded H2 database. If JdbcTemplate is in the classpath, then it will also configure a JdbcTemplate bean for us.
There's no need for us to worry about configuring those beans. They'll be configured for us, ready to inject into any of the beans we write.

There's a lot more to Spring Boot auto-configuration than embedded databases and JdbcTemplate.
There are several dozen ways that Spring Boot can take the burden of configuration off our hands,
including auto-configuration for the Java Persistence API (JPA), Thymeleaf templates, security, and Spring MVC.

### Starter Dependencies

Spring Boot offers help with project dependency management by way of starter dependencies.
Starter dependencies are really just special Maven/Gradle dependencies that take advantage of transitive dependency resolution
to aggregate commonly used libraries under a handful of feature-defined dependencies.

For e.g. if we want to build a REST API with Spring MVC that works with JSON resource representations + declarative validations as per JSR-303 + embedded Tomcat server
we will need at least the below 8 dependencies:

- org.springframework:spring-core
- org.springframework:spring-web
- org.springframework:spring-webmvc
- com.fasterxml.jackson.core:jackson-databind
- org.hibernate:hibernate-validator
- org.apache.tomcat.embed:tomcat-embed-core
- org.apache.tomcat.embed:tomcat-embed-el
- org.apache.tomcat.embed:tomcat-embed-logging-juli

Alternatively if we use Spring Boot we can achieve the above using a `single` dependency:

- org.springframework.boot:spring-boot-starter-web (Spring Boot "web" starter)

This single dependency will transitively pull in all of those other dependencies so we don't have to ask for them all.

#### Benefits:

1. Reduces the dependency count we need to add to our build files.
2. Simple to add new features. We can create a `web` app by using a `Web` starter, if we need to use JPA we can use the `JPA` starter, for security use `Security` starter.
3. Frees us from worrying about the versions of the dependencies used. Spring Boot pulls versions of the libraries that have been tested together and are hence compatible.

### Command-line interface

Spring Boot's CLI leverages starter dependencies and auto-configuration to let us focus on writing code.
Spring Boot's CLI is an optional piece of Spring Boot's power.
Although it provides tremendous power and simplicity for Spring development, it also introduces a rather unconventional development model.

### The Actuator

The Actuator offers the ability to inspect the internals of our application at runtime.
With the Actuator installed, we can inspect the inner workings of our application, including details such as:

- What beans have been configured in the Spring application context.
- What decisions were made by Spring Boot's auto-configuration.
- What environment variables, system properties, configuration properties, and command-line arguments are available to our application.
- The current state of the threads in and supporting our application.
- A trace of recent HTTP requests handled by our application.
- Various metrics pertaining to memory usage, garbage collection, web requests, and data source usage.

The Actuator exposes this information in two ways: via web endpoints or via a shell interface.
In the latter case, we can actually open a secure shell (SSH) into our application and issue commands to inspect our application as it runs.

## What Spring Boot Is NOT

1. Spring Boot is NOT an application server. 
   This misconception stems from the fact that it's possible to create web applications as self-executable JAR files that can be
   run at the command line without deploying applications to a conventional Java application server. 
   Spring Boot accomplishes this by embedding a servlet container (Tomcat, Jetty, or Undertow) within the application. 
   But it's the embedded servlet container that provides application server functionality, not Spring Boot itself.

2. Spring Boot doesn't implement any enterprise Java specifications such as JPA or JMS. 
   It does support several enterprise Java specifications, but it does so by automatically configuring beans in Spring that support those features. 
   For instance, Spring Boot doesn't implement JPA, but it does support JPA by auto-configuring the appropriate beans for a JPA implementation (such as Hibernate).

3. Spring Boot doesn't employ any form of code generation to accomplish its magic. 
   Instead, it leverages conditional configuration features from Spring 5, along with transitive dependency resolution offered by Maven and Gradle, 
   to automatically configure beans in the Spring application context.

Spring Boot is just Spring. Inside, Spring Boot is doing the same kind of bean configuration in Spring that we might do on our own if Spring Boot didn't exist. 
Thankfully, because Spring Boot does exist, we're freed from dealing with explicit boilerplate configuration and are able to focus on the logic that makes our application unique.

## Getting Started with Spring Boot

Spring Boot Project = Spring Project + Spring Boot Starters + Auto-Configuration

A few convenient options available for kickstarting our project with Spring Boot are listed below:

### Installing Spring Boot CLI

We can install the CLI:
- From a downloaded distribution.
- Using the Groovy Environment Manager.
- With OS X Homebrew.
- As a port using MacPorts.

Steps to install Spring Boot CLI using SDKMAN(run below commands on the command line):

> curl -s get.sdkman.io | bash
> source "/Users/habuma/.sdkman/bin/sdkman-init.sh"
> sdk install springboot
> spring --version

Commands to get list of available versions, installing a specific version, using one of the installed versions and setting a particular version as default are given below:

> sdk list springboot
> sdk install springboot 2.0.5.RELEASE
> sdk use springboot 2.0.5.RELEASE
> sdk default springboot 2.0.4.RELEASE

### Initializing Spring Boot Project with Spring initializer

Navigate to [Spring Starter](https://start.spring.io/) for more details on this.
Switch to full-version at the bottom of the page to get more details such as application name, list of dependencies, etc.

Spring Initializer can be used in several ways:

- Through a web-based interface.
- Via Spring Tool Suite.
- Via IntelliJ IDEA.
- Using the Spring Boot CLI.

In Spring Initializer create a project with below details:

1. Artifact: myapp
2. Package Name: myapp
3. Type: Gradle Project
4. Dependencies: Web and JPA

Using above details and clicking on `Generate Project` will create `myapp.zip` file for us.
Unzip the same and we will get a project which includes the below files:

- build.gradle - A Gradle build specification. Had we chosen a Maven project, this would be replaced with pom.xml.
- Application.java - A class with a main() method to bootstrap the application.
- ApplicationTests.java - An empty JUnit test class instrumented to load a Spring application context using Spring Boot auto-configuration.
- application.properties - An empty properties file for us to add configuration properties to as we see fit.
- `static` folder - To put any static content(JavaScript, stylesheets, images, etc.).
- `templates` folder - To put templates that render model data.

#### Using the Initializer from Spring Boot CLI:

To create a baseline Spring Boot project:

> spring init

This command will conclude by downloading a demo.zip file.
It creates a Maven-based project with only Spring Boot and testing dependencies.

To create application with Web/MVC, JPA, Security dependencies:

> spring init -dweb,jpa,security

The -d flag is for dependencies. 
`Note`: There should be NO space between `-d` and the dependencies.

If we want to create the same project as above but with Gradle as the build tool:

> spring init -dweb,jpa,security --build gradle

Since the default packaging for both Maven and Gradle is a `Jar` file. If we want packaging as `war`:

> spring init -dweb,jpa,security --build gradle -p war

If we want to specify a directory for the CLI to open the zip file in:

> spring init -dweb,jpa,security --build gradle -p war myapp

The last parameter above indicates that we want to extract the project into the `myapp` directory.

If we want to extract the project into the current directory:

> spring init -dweb,jpa,security --build gradle -p jar -x

If we want to know all the different parameters that the `init` command supports:

> spring help init

To find out what choices are available for the parameters listed in the above command:

> spring init --list

Whether we use Initializr's web-based interface, create our projects from Spring Tool Suite, 
or use the Spring Boot CLI to initialize a project, 
projects created using the Spring Boot Initializr have a familiar project layout, 
not unlike other Java projects we may have developed before.

## Developing Spring Boot Application

Two ways that Spring Boot has added a level of automation to Spring development: starter dependencies and automatic configuration.
These essential Spring Boot features free us from the tedium and distraction of enabling Spring in our projects
and let us focus on actually developing our applications.

### Creating a simple readinglist applcation

We will create a Spring Boot application with Spring MVC to handle web requests,
Thymeleaf to define web views, and Spring Data JPA to persist the reading selections to a database.
For now, that database will be an embedded H2 database.
We will write the application code in Java for now. And we'll use Gradle as our build tool of choice.

To create the application with the above starters/dependencies use:

> spring init -dweb,data-jpa,h2,thymeleaf --build gradle readinglist

The above command creates a project with the below files:

- build.gradle - The Gradle build specification.
- ReadingListApplication.java - The application's bootstrap class and primary Spring configuration class.
- application.properties - A place to configure application and Spring Boot properties.
- ReadingListApplicationTests.java - A basic integration test class.

`Bootstrapping Spring`

The `ReadingListApplication` class serves two purposes in a Spring Boot application:

configuration and bootstrapping.

First, it's the central Spring configuration class.
Even though Spring Boot auto-configuration eliminates the need for a lot of Spring configuration,
we will need at least a small amount of Spring configuration to enable auto-configuration.

```java

    package readinglist;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication // Enable component-scanning and auto-configuration
    public class ReadingListApplication {

        public static void main(String[] args) {
            SpringApplication.run(ReadingListApplication.class, args); // Bootstrap the application
        }
    }

```

The @SpringBootApplication annotation combines three other useful annotations:

1. Spring's @Configuration - Designates a class as a configuration class using Spring's Java-based configuration.
   When writing configuration, favor Java-based configuration over XML configuration.

2. Spring's @ComponentScan - Enables component-scanning so that the web controller classes and other components we write
   will be automatically discovered and registered as beans in the Spring application context.

3. Spring Boot's @EnableAutoConfiguration - This humble little annotation is the one line of configuration that
   enables the magic of Spring Boot auto-configuration.
   This one line keeps us from having to write the pages of configuration that would be required otherwise.

The main() method in the ReadingListApplication class enables us to run our application as an executable JAR file
from the command line. It passes a reference to the ReadingListApplication class to SpringApplication.run(),
along with the command-line arguments, to kick off the application.

Ways to build and run the application using Gradle:

> gradle bootRun

or

> gradle build

> java -jar build/libs/readinglist-0.0.1-SNAPSHOT.jar

Above commands will start a Tomcat server at port 8080.(we will still get 404 Not Found error since we do not have a controller for the root route.)

We will almost never need to change ReadingListApplication.java. 
If our application requires any additional Spring configuration beyond what Spring Boot auto-configuration provides, 
it's usually best to write it into separate @Configuration configured classes. (They'll be picked up and used by component-scanning.) 
In exceptionally simple cases, though, we could add custom configuration to ReadingListApplication.java.

### Testing Spring Boot Applications

The Initializr provides us with a skeleton test class: `ReadingListApplicationTests`.
This class is more than just a placeholder for tests - it also serves as an example of how to write tests for 
Spring Boot Applications.

```java

    package readinglist;
    import org.junit.Test;
    import org.junit.runner.RunWith;
    import org.springframework.boot.test.SpringApplicationConfiguration;
    import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
    import org.springframework.test.context.web.WebAppConfiguration;
    import readinglist.ReadingListApplication;
    @RunWith(SpringJUnit4ClassRunner.class)
    @SpringApplicationConfiguration(classes = ReadingListApplication.class) // Loading context via Spring Boot
    @WebAppConfiguration
    public class ReadingListApplicationTests {

        @Test
        public void contextLoads() {
        // test that the context loads
        }
    }

```

In a typical Spring integration test, 
we would annotate the test class with @ContextConfiguration to specify how the test should load the Spring application context.

But in order to take full advantage of Spring Boot magic, the @SpringApplicationConfiguration annotation should be used instead.
ReadingListApplicationTests is annotated with @SpringApplicationConfiguration
to load the Spring application context from the ReadingListApplication configuration class.

`ReadingListApplicationTests` also includes one simple test method, contextLoads(). 
It's so simple, in fact, that it's an empty method. 
But it's sufficient for the purpose of verifying that the application context loads without any problems. 
If the configuration defined in ReadingListApplication is good, the test will pass. If there are any problems, the test will fail.
This test method verifies every bit of functionality provided by the application at this point.

#### Configuring Application.properties

The application.properties file given to us by the Initializr is initially empty. 
In fact, this file is completely optional, so we could remove it completely without impacting the application. 
But there's also no harm in leaving it in place.

If we want to poke around with application.properties, we can add the following line:

```properties

server.port=8000

```

With this line, we are configuring the embedded Tomcat server to listen on port 8000 instead of the default port 8080.
We can confirm this by running the application again.
This demonstrates that the application.properties file comes in handy for 
fine-grained configuration of the stuff that Spring Boot automatically configures.
But we can also use it to specify properties used by application code.

The main thing to notice is that at no point do we explicitly ask Spring Boot to
load application.properties for us. By virtue of the fact that application.properties
exists, it will be loaded and its properties made available for configuring both Spring
and application code.

### Dissecting a Spring Boot project build

Spring Boot provides build plugins for both Gradle and Maven to assist in building Spring Boot projects.

```gradle

    buildscript {
    	ext {
    		springBootVersion = '2.0.5.RELEASE'
    	}

    	repositories {
    		mavenCentral()
    	}

    	dependencies {
    		classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}") // depend on Spring Boot plugin
    	}
    }

    apply plugin: 'java'
    apply plugin: 'eclipse'
    apply plugin: 'org.springframework.boot' // apply spring boot plugin
    apply plugin: 'io.spring.dependency-management'

    group = 'com.manning'
    version = '0.0.1-SNAPSHOT'
    sourceCompatibility = 1.8

    repositories {
    	mavenCentral()
    }


    dependencies {
        // starter dependencies
    	implementation('org.springframework.boot:spring-boot-starter-data-jpa')
    	implementation('org.springframework.boot:spring-boot-starter-thymeleaf')
    	implementation('org.springframework.boot:spring-boot-starter-web')
    	runtimeOnly('com.h2database:h2')
    	testImplementation('org.springframework.boot:spring-boot-starter-test')
    }

```

Whether we choose Gradle or Maven, Spring Boot's build plugins contribute to the build in a few ways.

We can use the bootRun task to run the application with Gradle.
Similarly, the Spring Boot Maven plugin provides a `spring-boot:run` goal that achieves the same thing if we are using a Maven build.

The main feature of the build plugins is that they are able to package the project as an executable *uber-JAR*.
This includes packing all of the application's dependencies within the JAR and
adding a manifest to the JAR with entries that make it possible to run the application with `java -jar`.

## Using starter dependencies

Without Spring Boot starter dependencies, we have got some homework to do.
All we want to do is develop a Spring web application with Thymeleaf views that persists its data via JPA.
But before we can even write our first line of code, 
we have to go figure out what needs to be put into the build specification to support our plan.

### Specifying facet-based dependencies

Spring Boot addresses project dependency complexity by providing several dozen "starter" dependencies.
A starter dependency is essentially a Maven POM that defines transitive dependencies on other libraries
that together provide support for some functionality.
any of these starter dependencies are named to indicate the facet or kind of functionality they provide.

For example, the reading-list application is going to be a web application.
Rather than add several individually chosen library dependencies to the project build,
it is much easier to simply declare that this is a web application.
We can do that by adding Spring Boot's web starter to the build.
We also want to use Thymeleaf for web views and persist data with JPA.
Therefore, we need the Thymeleaf and Spring Data JPA starter dependencies in the build.
For testing purposes, we also want libraries that will enable us to run integration
tests in the context of Spring Boot. Therefore, we also want a test-time dependency on Spring Boot's test starter.

Taken altogether, we have the following five dependencies that the Initializr provided in the Gradle build:

```gradle

    dependencies {
        compile "org.springframework.boot:spring-boot-starter-web"
        compile "org.springframework.boot:spring-boot-starter-thymeleaf"
        compile "org.springframework.boot:spring-boot-starter-data-jpa"
        compile "com.h2database:h2"
        testCompile("org.springframework.boot:spring-boot-starter-test")
    }

```

Via transitive dependencies, adding these four dependencies
is the equivalent of adding several dozen individual libraries to the build.
Some of those transitive dependencies include such things as Spring MVC, Spring Data JPA, Thymeleaf,
as well as any transitive dependencies that those dependencies declare.

In no case did we need to specify the version.
The versions of the starter dependencies themselves are determined by the version of Spring Boot we are using.
The starter dependencies themselves determine the versions of the various transitive dependencies that they pull in.
Be encouraged to know that Spring Boot has been tested to ensure that all of the dependencies pulled in are compatible with each other.
It is actually very liberating to just specify a starter dependency and not have to worry about which libraries
and which versions of those libraries we need to maintain.

To know the dependencies that our project uses we can rely on the build tool.
In the case of Gradle, the `dependencies` task will give us a dependency tree
that includes every library our project is using and their versions:

> gradle dependencies

We can get a similar dependency tree from a Maven build with the tree goal of the dependency plugin:

> mvn dependency:tree

### Overriding starter transitive dependencies

We can use the facilities provided by the build tool to selective override transitive dependencies if needed.
For e.g. when using the Spring Boot `Web` starter if we want to exclude the Jackson JSON library,
we can do so by adding the below to our build.gradle file:

```gradle

    compile("org.springframework.boot:spring-boot-starter-web") {
        exclude group: 'com.fasterxml.jackson.core'
    }

```

To use a specific Jackson version:

```gradle

    compile("com.fasterxml.jackson.core:jackson-databind:2.4.3")

```

To use an older version of Jackson instead of the one that is transitively resolved by Spring Boot Web starter:

```gradle

    compile("org.springframework.boot:spring-boot-starter-web") {
        exclude group: 'com.fasterxml.jackson.core'
    }
    
    compile("com.fasterxml.jackson.core:jackson-databind:2.3.1")

```

`Note`: Be *careful* in the above case as the version that we specifically choose may not work well with other transitively pulled dependencies.

## Using automatic configuration

Spring Boot auto-configuration is a runtime (more accurately, application startup-time) process
that considers several factors to decide what Spring configuration should and should not be applied.

For e.g.

- Is Spring's JdbcTemplate available on the classpath? If so and if there is a Data-Source bean, then auto-configure a JdbcTemplate bean.
- Is Thymeleaf on the classpath? If so, then configure a Thymeleaf template resolver, view resolver, and template engine.
- Is Spring Security on the classpath? If so, then configure a very basic web security setup.

There are nearly 200 such decisions that Spring Boot makes with regard to auto-configuration every time an application starts up,
covering areas such as security, integration, persistence, and web development. All of this auto-configuration serves to
keep us from having to explicitly write configuration unless absolutely necessary.

### Focusing on application functionality

Spring Boot auto-configuration keeps us focused on writing application code.

The best way to demonstrate how auto-configuration works and helps us when creating applications with Spring Boot
is to write application code.

#### Defining the domain

The central domain concept in our application is a book that's on a reader's reading list.

```java

    package readinglist;
    import javax.persistence.Entity;
    import javax.persistence.GeneratedValue;
    import javax.persistence.GenerationType;
    import javax.persistence.Id;
    
    @Entity
    public class Book {
    
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Long id;
    private String reader;
    private String isbn;
    private String title;
    private String author;
    private String description;
    
    public Long getId() {
        return id;
    }
    
    public void setId(Long id) {
        this.id = id;
    }
    
    public String getReader() {
        return reader;
    }
    
    public void setReader(String reader) {
        this.reader = reader;
    }
    
    public String getIsbn() {
        return isbn;
    }
    
    public void setIsbn(String isbn) {
        this.isbn = isbn;
    }
    
    public String getTitle() {
        return title;
    }
    
    public void setTitle(String title) {
        this.title = title;
    }
    
    public String getAuthor() {
        return author;
    }
    
    public void setAuthor(String author) {
        this.author = author;
    }
    
    public String getDescription() {
        return description;
    }
    
    public void setDescription(String description) {
        this.description = description;
    }
    
    }

```

The `Book` class is a simple Java object with a handful of properties describing a book and the necessary accessor methods.
It's annotated with *@Entity* designating it as a `JPA entity`.
The id property is annotated with *@Id* and *@GeneratedValue* to indicate that this field is the entity's identity and that its value will be automatically provided.

#### Defining the repository interface

Define the repository through which the ReadingList objects will be persisted to the database.
Because we are using Spring Data JPA, that task is a simple matter of creating an interface that extends Spring Data JPA's `JpaRepository` interface.

```java

    package readinglist;
    import java.util.List;
    import org.springframework.data.jpa.repository.JpaRepository;
    
    public interface ReadingListRepository extends JpaRepository<Book, Long> {
        List<Book> findByReader(String reader);
    }

```

By extending JpaRepository, ReadingListRepository inherits 18 methods for performing common persistence operations.

The JpaRepository interface is parameterized with two parameters: 

1. the domain type that the repository will work with, and
2. the type of its ID property.

In addition, we have added a findByReader() method through which a reading list can be looked up given a reader's username.

Spring Data provides a special magic of its own, making it possible to define a repository with just an interface.
The interface will be implemented automatically at runtime when the application is started.

#### Creating the Web Interface

Now that we have the application's domain defined and a repository for persisting objects
from that domain to the database, all that's left is to create the web front-end.
A Spring MVC controller given below will handle HTTP requests for the application:

```java

    package readinglist;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.RestController;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestMethod;
    import java.util.List;
    
    @RestController
    @RequestMapping("/")
    public class ReadingListController {
    
    private ReadingListRepository readingListRepository;
    
    @Autowired
    public ReadingListController(ReadingListRepository readingListRepository) {
        this.readingListRepository = readingListRepository;
    }
    
    @RequestMapping(value="/{reader}", method=RequestMethod.GET)
    public String readersBooks(@PathVariable("reader") String reader, Model model) {
        List<Book> readingList = readingListRepository.findByReader(reader);
        if (readingList != null) {
            model.addAttribute("books", readingList);
        }
        return "readingList";
    }
    
    @RequestMapping(value="/{reader}", method=RequestMethod.POST)
    public String addToReadingList(@PathVariable("reader") String reader, Book book) {
        book.setReader(reader);
        readingListRepository.save(book);
        return "redirect:/{reader}";
    }
    }

```

