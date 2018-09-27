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
There’s no need for us to worry about configuring those beans. They'll be configured for us, ready to inject into any of the beans we write.

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

