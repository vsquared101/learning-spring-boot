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

    >   spring run HelloController.groovy

