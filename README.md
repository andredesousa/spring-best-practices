# Spring Best Practices

Spring is an application framework and inversion of control container for the Java platform.
These tips are based on Spring documentation, books, articles and professional experience.

## Table of Contents

1. [Follow code conventions](#follow-code-conventions)
2. [Follow a package naming convention](#follow-a-package-naming-convention)
3. [Use Maven/Gradle wrapper](#use-mavengradle-wrapper)
4. [Use Spring Cloud](#use-spring-cloud)
5. [Use Spring Boot starters](#use-spring-boot-starters)
6. [Use OpenAPI and Swagger UI](#use-openapi-and-swagger-ui)
7. [Use code generators](#use-code-generators)
8. [Use database migrations](#use-database-migrations)
9. [Use controllers only for routing](#use-controllers-only-for-routing)
10. [Use services for business logic](#use-services-for-business-logic)
11. [Use repository pattern](#use-repository-pattern)
12. [Use validators](#use-validators)
13. [Use DTOs](#use-dtos)
14. [Use caching](#use-caching)
15. [Provide a global exception handling](#provide-a-global-exception-handling)
16. [Avoid global state and mutability](#avoid-global-state-and-mutability)
17. [Remove unused code](#remove-unused-code)
18. [Set logging levels](#set-logging-levels)
19. [Expose health checks and metrics](#expose-health-checks-and-metrics)
20. [Externalize all configurations](#externalize-all-configurations)
21. [Analyse your code](#analyse-your-code)
22. [Check dependencies for vulnerabilities](#check-dependencies-for-vulnerabilities)
23. [Compile natively](#compile-natively)
24. [Prefer Spring WebFlux](#prefer-spring-webflux)
25. [Test your code](#test-your-code)

## Follow code conventions

Code conventions are base rules that allow the creation of a uniform code base across an organization.
Following them does not only increase the uniformity and therefore the quality of the code.
[Oracle Code Conventions](https://www.oracle.com/java/technologies/javase/codeconventions-contents.html) and [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html) are the two main coding styles for Java.
[Checkstyle](https://checkstyle.sourceforge.io/) is a tool to help programmers find class design problems, method design problems, and others.
It also can check code layout and formatting issues.
[Prettier](https://prettier.io/) combined with [Spotless](https://github.com/diffplug/spotless) can be used to enforce a consistent code style.
Code conventions must be dynamic and adaptable for each team and project.

## Follow a package naming convention

Proper packaging will help to understand the code and the flow of the application easily.
You can structure your application with meaningful packaging.
You can use the following naming convention for your packages:

- The `entity` package contains the database entities of the application.
- The `repository` package contains all the repositories-related classes.
- The `service` package contains all the business logic-related classes.
- The `controller` package contains all controllers classes of the application.
- Other common packages are `config`, `mapper`, `filter`, `exception`, etc.

This style is very convenient in small-size microservices.
If you are working on a huge code base, a feature-based approach can be used.

## Use Maven/Gradle wrapper

The recommended way to execute any Maven/Gradle build is with the help of the wrapper ([Maven Wrapper](https://maven.apache.org/wrapper/) and [Gradle Wrapper](https://docs.gradle.org/current/userguide/gradle_wrapper.html)).
Instead of installing many versions of it in the operating system, you can just use the project-specific wrapper script.
The wrapper is a script that invokes a declared version of Maven/Gradle, downloading it beforehand if necessary.
As a result, developers can get up and running with a Maven/Gradle project quickly without having to follow manual installation processes saving time.

## Use Spring Cloud

[Spring Cloud](https://spring.io/projects/spring-cloud) provides tools for developers to quickly build some of the common patterns in distributed systems (e.g. configuration management, service discovery, circuit breakers, intelligent routing, micro-proxy, control bus, one-time tokens, global locks, leadership election, distributed sessions, cluster state).
Coordination of distributed systems leads to boiler plate patterns, and using Spring Cloud developers can quickly stand-up services and applications that implement those patterns.
They will work well in any distributed environment, including the developer's own laptop, bare metal data centres, and managed platforms.

## Use Spring Boot starters

Dependency management is a critical aspect of any complex project. Spring Boot provides several starters that allow you to add JARs in the classpath.
In the Spring Boot, all the starters follow a similar naming pattern: `spring-boot-starter-*`, where `*` denotes a particular type of starter.
You can use [Spring Initializr](https://start.spring.io/) to help you create a new Spring Boot project and choose possible dependencies according to your needs.
Furthermore, you can add [spring-boot-devtools](https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.devtools) dependency to take advantage of development-time features provided by the Developer Tools, such as automatic restart, LiveReload and global Settings.

## Use OpenAPI and Swagger UI

[OpenAPI Specification](https://swagger.io/specification/) is the factual standard for creating REST APIs.
An OpenAPI definition can then be used by documentation generation tools to display the API, code generation tools to generate servers and clients in various programming languages, testing tools, and many other use cases.
[Swagger UI](https://swagger.io/tools/swagger-ui/) allows anyone to visualize and interact with the API's resources without having any of the implementation logic in place.
It's automatically generated from your OpenAPI Specification.
You just need to add [springdoc-openapi-ui](https://mvnrepository.com/artifact/org.springdoc/springdoc-openapi-ui) dependency to your project to automate the generation of API documentation.

## Use code generators

Java is a great language, but it can sometimes get too verbose for common tasks.
[Lombok](https://projectlombok.org/) is a Java library that is used to minimize or remove the boilerplate code.
Just by using the annotations, you can save space and readability of the source code.
[MapStruct](https://mapstruct.org/) is a code generator that greatly simplifies the implementation of mappings between Java bean types based on a convention over configuration approach.
Also, OpenAPI Specification can be used by code generation tools to generate servers and clients for Java and Spring applications.
For that, you can use [OpenAPI Generator](https://openapi-generator.tech/docs/plugins) plugin for Maven/Gradle.

## Use database migrations

Database migrations is a process of making changes to database schema during a development process.
You wouldn't develop app code without version control.
The same should be true for database changes. Migrations are most commonly written in SQL.
Spring Boot supports [Flyway](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto.data-initialization.migration-tool.flyway) and [Liquibase](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto.data-initialization.migration-tool.liquibase) migration tools.
If you are using Liquibase, you will have defined your database as a set of Liquibase change sets, in XML, YAML, or JSON , it can be easily used as a source of meta information by the [jOOQ](https://www.jooq.org/) code generator.

## Use controllers only for routing

Controllers are dedicated to routing. Controllers are the ultimate target of requests, then requests will be handed over to the service layer and processed by the service layer.
They are stateless and all business logic should not place on them. Controllers should deal with the HTTP layer of the application and oriented around a business capability.
See the code snippet of a controller:

```java
@RestController
@RequestMapping("/user")
public class UserController {
    @Autowired
    private UserService userService;

    @GetMapping
    public List<User> findAll() {
        return userService.findAll();
    }
}
```

## Use services for business logic

The business logic of the application goes here with validations, caching, etc. Build your services around business capabilities/domains/use-cases. Services communicate with the persistence layer and receive the results. Services are singleton and annotated with `@Service`. See the code snippet of a service:

```java
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;

    public List<User> findAll() {
        return userRepository.findAll();
    }
}
```

## Use repository pattern

Repositories are a very popular pattern for Java-based persistence layers.
They encapsulate the database operations you can perform on entity objects and aggregates.
That helps to separate the business logic from the database operations and improves the reusability of your code.
[Spring Data JPA](https://spring.io/projects/spring-data-jpa) provides repository support for the Jakarta Persistence API (JPA).
See the code snippet of a repository:

```java
@Repository
public interface UserRepository extends JpaRepository<User, Integer> {}
```

## Use validators

REST APIs need to validate the data it receives, and Spring provides rich built-in support for validating REST API request objects.
[Spring Boot Starter Validation](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-validation) offers standard validation primitives such as `@NotNull`, `@NotBlank`, `@Size`, or `@Email` and if you need anything more advanced, you can easily implement your custom annotations.
See the code snippet of a DTO:

```java
public class User {
    @NotNull
    @Size(min = 4, max = 20)
    public String username;

    @NotNull
    @Size(min = 8, max = 20)
    public String password;
}
```

## Use DTOs

While it is also possible to directly expose the database entities on REST endpoints to send/receive client data, this is not the best approach.
It creates high coupling between the persistence models and the API models.
The better approach is defining a separate [Data Transfer Object](https://www.baeldung.com/java-dto-pattern) (DTO) that represents the API resource class which is mapped from a database entity or multiple entities. To do this mapping, you can use [MapStruct](https://mapstruct.org/).
With DTOs, you can build different views from your domain models, allowing you to create other representations of the same domain but optimizing them to the clients' needs without affecting your domain design.

## Use caching

Caching is an important factor when talking about application performance.
If you use Spring Boot, then you can utilize the [spring-boot-starter-cache](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-cache) dependency to easily add the caching dependencies to your project.
You can enable the caching feature simply by adding the `@EnableCaching` annotation to any of the configuration classes.
Once you have enabled caching, the next step is to bind the caching behaviour to the methods with declarative annotations.
When caching is enabled, then the application first looks for required object in the cache instead of fetching it from the source.
If you are not satisfied with default caching, you can use [Hazelcast](https://hazelcast.com/), [Redis](https://redis.io/), or any other distributed caching implementations.

## Provide a global exception handling

Besides the classic 404 error page, you should also look at what our application returns in case of an uncaught exception.
Normally, exceptions will be translated to a 500 error (Internet Server Error) and written to the log.
`@ControllerAdvice` is an annotation provided by Spring allowing you to write global code that can be applied to your controllers.
See the code snippet of a Global Exception Handler:

```java
@ControllerAdvice
public class GlobalExceptionHandler extends ResponseEntityExceptionHandler {
    @ExceptionHandler(IOException.class)
    protected ResponseEntity<Object> handleIOException(RuntimeException ex, WebRequest request) {
        return handleExceptionInternal(ex, "IOException handler executed", new HttpHeaders(), HttpStatus.NOT_FOUND, request);
    }
}
```

Also, you should set `server.error.include-stacktrace=never` for production environments to avoid including stack trace in the server error.

## Avoid global state and mutability

Problems caused by parallel execution of programs are nerve-wrackingly elusive and often times extremely difficult to debug.
First, always remember the "global state" issue.
If you're creating a multithreaded application, absolutely anything that is globally modifiable should be closely monitored and, if possible, removed altogether.
Immutability comes directly from functional programming and, adapted to OOP, states that class mutability and changing state should be avoided.
This, in short, means foregoing setter methods and having private final fields on all your model classes.
This way you can be certain that no contention problems arise and that accessing object properties will provide the correct values at all times.

## Remove unused code

Unused code or dead code is any code which will never be executed.
It may be some condition, loop or any file which was simply created but wasn't used in our project.
It is a problem because that code has no sense, and you can drop it. Less code also increases maintenance, IDE performance and makes it easier to understand.
The quickest way to find dead code is to use a good IDE.
You can delete unused code and unneeded files. Also, you can delete unnecessary classes and parameters.
Tools like [OpenRewrite](https://docs.openrewrite.org/) or [Spotless](https://github.com/diffplug/spotless) to remove unused code.

## Set logging levels

Logs are supposed to be a consistent and reliable source of information, which makes troubleshooting systems easier.
Sometimes the logs provide too much information and other times they do not provide enough data.
To set the [logging level](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.logging.log-levels) in a Spring Boot application, you can change the logging settings in the *application.properties* file.
All the supported logging systems can have logger levels configured using `logging.level.<logger-name>=<level>`, where the level is one of ERROR, WARN, INFO, DEBUG, or TRACE, or OFF.
By default, ERROR, WARN, and INFO level messages are logged.
In production environments you should avoid DEBUG or TRACE levels.

## Expose health checks and metrics

Spring Boot supports you with readiness/liveness health checks via [Spring Boot Actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html).
It allows applications to provide information about their state to external viewers which is typically useful in cloud environments where automated processes must be able to determine whether the application should be discarded or restarted.
Depending on the HTTP status code returned from a GET request, the agent will act when it receives an "unhealthy" response.
Spring Boot Actuator provides dependency management and auto-configuration for [Micrometer](https://micrometer.io/), an application metrics facade that supports numerous monitoring systems.
These metrics can be read remotely to be processed by additional tools such as [Prometheus](https://prometheus.io/) and stored for analysis and visualization.

## Externalize all configurations

Spring Boot allows you to externalize your configuration so you can work with the same application code in different environments.
Spring Boot reads [configuration properties](https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html) from system properties, environment variables, and *application.properties* in descending ordinal. Spring Boot supports different properties based on the Spring active profile.
You can define profile-specific files like `application-{profile}.properties`.
Also, you can use [Spring Cloud Config](https://docs.spring.io/spring-cloud-config/docs/current/reference/html/) to externalize application configuration files at runtime and have a central place to manage these properties across all environments.

## Analyse your code

To ensure long-term code maintainability, you should follow best coding practices and style guide rules.
A linter is a static code analysis tool used to flag programming errors, bugs, stylistic errors, and suspicious constructs.
[Checkstyle](https://checkstyle.sourceforge.io/) is a tool to help programmers find class design problems, method design problems, and others.
[PMD](https://pmd.github.io/) finds common programming flaws like unused variables, empty catch blocks, unnecessary object creation, and so forth.
[SpotBugs](https://spotbugs.github.io/) is used to perform static analysis on Java code.
It looks for instances of "bug patterns".
You can combine these tools to achieve better results.
These checks can also be done by [SonarQube](https://www.sonarqube.org/).

## Check dependencies for vulnerabilities

It is important to ensure that there are no known vulnerabilities throughout your application's dependency tree.
Therefore, you should frequently update your application's dependencies to the latest versions.
You can use [OWASP Dependency-Check](https://owasp.org/www-project-dependency-check/) to detect publicly disclosed vulnerabilities contained within a project's dependencies.
To implement it, just add [dependency-check-maven](https://jeremylong.github.io/DependencyCheck/dependency-check-maven/index.html) in your *pom.xml* file or [org.owasp.dependencycheck](https://jeremylong.github.io/DependencyCheck/dependency-check-gradle/index.html) in your *build.gradle* file.
When you choose a base image for your project, you indirectly assume the risk of all the container security concerns that the base image is bundled with.
[Trivy](https://github.com/aquasecurity/trivy) is a simple and comprehensive vulnerability scanner for Docker images and other artifacts.

## Compile natively

Native images provide key advantages, such as instant startup, instant peak performance, and reduced memory consumption.
The native executable for your application will contain the application code, required libraries, Java APIs, and a reduced version of a VM.
To package a Spring Boot application into a native executable, you need to use [Spring Native](https://docs.spring.io/spring-boot/docs/current/reference/html/native-image.html) and [Native Build Tools](https://graalvm.github.io/native-build-tools/latest/index.html).
Native Build Tools are plugins shipped by GraalVM for both Maven and Gradle.
When writing native image applications, it is recommended that you continue to use the JVM whenever possible to develop the majority of your unit and integration tests.
This will help keep developer build times down and allow you to use existing IDE integrations.

## Prefer Spring WebFlux

Reactive programming is a programming paradigm where the focus is on developing asynchronous and non-blocking applications.
One of the main reasons why developers should switch from blocking to non-blocking code is efficiency.
Spring 5 introduced [Spring WebFlux](https://docs.spring.io/spring-framework/reference/web/webflux.html) to support the reactive web in a non-blocking manner.
Spring WebFlux is based on the [Reactor API](https://projectreactor.io/), just another awesome implementation of the reactive stream.
Spring WebFlux supports annotation-based configurations in the same way as the Spring Web MVC framework.
You just need to add [spring-boot-starter-webflux](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-webflux) dependency to your project to start coding.

## Test your code

If you have no tests or an inadequate amount, then every time you ship code, you won't be sure that you didn't break anything.
Always write tests for every new feature/module you introduce.
Spring Boot provides the `@SpringBootTest` annotation which you can use to create an application context containing all the objects you need for all test types.
Most developers will just use [spring-boot-starter-test](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-test) which imports both Spring Boot test modules as well has [JUnit](https://junit.org/junit5/), [Mockito](https://site.mockito.org/), [AssertJ](https://assertj.github.io/doc/) and several other useful libraries.
If you've written tests with Spring or Spring Boot in the past, you'll probably notice that you don't need Spring to write unit tests.
You just need to use JUnit and Mockito.
It is also recommended that you keep integration tests separate from unit tests and not run them alongside unit tests.

## Bibliography

- [16 Best Practices for Spring Boot in Production](https://www.springcloud.io/post/2022-08/springboot-best-practices/)
- [A Guide to Caching in Spring](https://www.baeldung.com/spring-cache-tutorial)
- [Best Practices for How to Test Spring Boot Applications](https://tanzu.vmware.com/developer/guides/spring-boot-testing/)
- [Best Practices for Structuring Spring Boot Application](https://www.geeksforgeeks.org/best-practices-for-structuring-spring-boot-application/)
- [Database Migrations with Flyway](https://www.baeldung.com/database-migrations-with-flyway)
- [Documenting a Spring REST API Using OpenAPI 3.0](https://www.baeldung.com/spring-rest-openapi-documentation)
- [Error Handling for REST with Spring](https://www.baeldung.com/exception-handling-for-rest-with-spring)
- [Guide to Spring 5 WebFlux](https://www.baeldung.com/spring-webflux)
- [Introduction to Spring Data JPA](https://www.baeldung.com/the-persistence-layer-with-spring-data-jpa)
- [Java Bean Validation](https://docs.spring.io/spring-framework/reference/core/validation/beanvalidation.html)
- [Logging in Spring Boot](https://www.baeldung.com/spring-boot-logging)
- [Properties with Spring and Spring Boot](https://www.baeldung.com/properties-with-spring)
- [Spring Boot Microservices Coding Style Guidelines and Best Practices](https://medium.com/codex/spring-boot-microservices-coding-style-guidelines-and-best-practices-1dec229161c8)
- [Spring Boot Starters](https://www.javatpoint.com/spring-boot-starters)
- [Spring Boot Tips, Tricks and Techniques](https://piotrminkowski.com/2021/01/13/spring-boot-tips-tricks-and-techniques/)
- [Spring Profiles](https://www.baeldung.com/spring-profiles)
- [Testing in Spring Boot](https://www.baeldung.com/spring-boot-testing)
- [Tips, Tricks, and Springs: Best Spring Practices](https://wearenotch.com/tips-tricks-and-springs-best-spring-practices/)
- [Top 10 Spring Security best practices for Java developers](https://www.synopsys.com/blogs/software-security/spring-security-best-practices/)
- [Top Spring Framework Mistakes](https://www.toptal.com/spring/top-10-most-common-spring-framework-mistakes)
- [Use Liquibase to Safely Evolve Your Database Schema](https://www.baeldung.com/liquibase-refactor-schema-of-java-app)
