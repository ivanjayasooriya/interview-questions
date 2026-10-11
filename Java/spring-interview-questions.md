# Spring Framework Interview Questions & Answers — Complete Guide

A comprehensive reference covering Spring Core, Spring Boot, Spring MVC, Spring Data JPA, Spring Security, AOP, transactions, testing, microservices, and reactive programming.

---

## Table of Contents
1. [Spring Core Basics](#1-spring-core-basics)
2. [IoC Container & Dependency Injection](#2-ioc-container--dependency-injection)
3. [Bean Lifecycle & Scopes](#3-bean-lifecycle--scopes)
4. [Configuration Approaches](#4-configuration-approaches)
5. [Spring AOP](#5-spring-aop)
6. [Spring Boot](#6-spring-boot)
7. [Spring MVC](#7-spring-mvc)
8. [REST API Development](#8-rest-api-development)
9. [Spring Data JPA](#9-spring-data-jpa)
10. [Transaction Management](#10-transaction-management)
11. [Spring Security](#11-spring-security)
12. [Exception Handling](#12-exception-handling)
13. [Spring Testing](#13-spring-testing)
14. [Spring Boot Actuator & Observability](#14-spring-boot-actuator--observability)
15. [Microservices & Spring Cloud](#15-microservices--spring-cloud)
16. [Reactive Programming (Spring WebFlux)](#16-reactive-programming-spring-webflux)
17. [Messaging & Integration](#17-messaging--integration)
18. [Caching](#18-caching)
19. [Profiles, Properties & Environment](#19-profiles-properties--environment)
20. [Best Practices & Common Pitfalls](#20-best-practices--common-pitfalls)

---

## 1. Spring Core Basics

**Q1. What is the Spring Framework, and what problem does it solve?**
Spring is a comprehensive framework for building Java applications, built around **Inversion of Control (IoC)** and **Dependency Injection (DI)**. It solves the problem of tight coupling between application components by managing object creation and wiring centrally (the IoC container), and provides consistent abstractions over common infrastructure concerns (transactions, data access, web MVC, security) so developers focus on business logic rather than boilerplate/plumbing code.

**Q2. What are the core modules of the Spring Framework?**
- **Core Container:** Beans, Core, Context, SpEL (Spring Expression Language).
- **Data Access/Integration:** JDBC, ORM, Transactions, JMS.
- **Web:** Spring MVC, WebFlux, WebSocket.
- **AOP & Instrumentation:** aspect-oriented programming support.
- **Test:** support for unit/integration testing with JUnit/TestNG.
Spring Boot, Spring Data, Spring Security, and Spring Cloud are separate, related projects built on top of Spring Framework.

**Q3. What is Inversion of Control (IoC)?**
A design principle where the control of object creation and lifecycle management is transferred from the application code to a container/framework — instead of a class instantiating its own dependencies (`new Service()`), the container creates and provides them. This inverts the traditional flow of control, promoting loose coupling.

**Q4. What is Dependency Injection (DI), and how does it relate to IoC?**
DI is the specific *implementation technique* through which IoC is achieved in Spring — dependencies are "injected" into a class from the outside (via constructor, setter, or field) rather than the class creating them itself. IoC is the broader principle; DI is how Spring implements it.

**Q5. What are the types of Dependency Injection in Spring?**
- **Constructor injection:** dependencies passed via the constructor — recommended by the Spring team; enables immutability (`final` fields), makes required dependencies explicit, and fails fast if a dependency is missing.
- **Setter injection:** dependencies set via setter methods — useful for optional dependencies.
- **Field injection:** dependencies injected directly into fields via `@Autowired` — discouraged in production code because it hides dependencies, makes classes harder to test/instantiate without the container, and doesn't support immutability.

**Q6. What is the Spring IoC container, and what are its two main types?**
The core component responsible for instantiating, configuring, and managing the lifecycle of beans. Two implementations:
- **BeanFactory:** the basic, lightweight container providing DI functionality (lazy initialization by default).
- **ApplicationContext:** a more feature-rich superset of `BeanFactory` — adds event propagation, AOP integration, internationalization (i18n), and eager singleton initialization by default. Used in virtually all modern applications.

---

## 2. IoC Container & Dependency Injection

**Q7. What is a Spring Bean?**
An object that is instantiated, assembled, and managed by the Spring IoC container. Beans are defined via configuration (XML, Java `@Configuration` classes, or component scanning with stereotype annotations) and their dependencies, scope, and lifecycle are controlled by the container.

**Q8. What are stereotype annotations, and what's the difference between them?**
Annotations marking a class as a Spring-managed component, enabling auto-detection via classpath scanning:
- `@Component`: generic stereotype for any Spring-managed component.
- `@Service`: marks a service-layer (business logic) component — semantically identical to `@Component` but improves code readability/intent.
- `@Repository`: marks a data access layer component — additionally enables automatic translation of persistence-specific exceptions into Spring's unified `DataAccessException` hierarchy.
- `@Controller`/`@RestController`: marks a Spring MVC web controller (`@RestController` = `@Controller` + `@ResponseBody`, for REST APIs).

**Q9. What is `@Autowired`, and how does autowiring resolution work?**
Marks a dependency for automatic injection by the Spring container. Resolution order: **by type** first — if exactly one bean of the required type exists, it's injected; if multiple candidates exist, Spring attempts to resolve **by name** (matching the field/parameter name to a bean name), or you can disambiguate explicitly with `@Qualifier("beanName")`. If no matching bean is found and the dependency is required, Spring throws `NoSuchBeanDefinitionException` (unless marked `required = false` or using `Optional<T>`).

**Q10. What is the difference between `@Qualifier` and `@Primary`?**
`@Primary` marks one bean among multiple candidates of the same type as the **default** choice when autowiring is ambiguous — used at the bean definition site. `@Qualifier("name")` is used at the **injection point** to explicitly specify which named bean to inject, overriding the default `@Primary` choice if both are used together for different targets.

**Q11. What is circular dependency, and how does Spring handle it?**
When two or more beans depend on each other (directly or transitively), forming a cycle (Bean A needs Bean B, which needs Bean A). Spring can resolve circular dependencies for **setter/field injection** using early bean references (exposing a partially-constructed bean via a cache during singleton creation), but **cannot** resolve circular dependencies for **constructor injection** (throws `BeanCurrentlyInCreationException`) since a fully-constructed instance is required upfront. Best practice: redesign to avoid circular dependencies (e.g., extract shared logic into a third bean) rather than relying on the setter-injection workaround.

**Q12. What is `ApplicationContext`, and how do you obtain a bean from it programmatically?**
The central interface for accessing the Spring IoC container's configuration and beans. Typically obtained automatically when using Spring Boot, but can be accessed programmatically via `context.getBean(MyService.class)` or `context.getBean("beanName", MyService.class)` — though directly pulling beans this way (Service Locator style) is generally discouraged in favor of DI.

**Q13. What is the difference between `BeanFactory` and `ApplicationContext`?**
| | BeanFactory | ApplicationContext |
|---|---|---|
| Initialization | Lazy (beans created on first request) | Eager (singletons created at startup, by default) |
| AOP support | No | Yes |
| Event publishing | No | Yes (`ApplicationEvent`) |
| i18n support | No | Yes (`MessageSource`) |
| Use case | Rarely used directly | Standard choice for all Spring applications |

**Q14. What is Spring's `@Value` annotation used for?**
Injects values from property files, environment variables, or SpEL expressions directly into fields/parameters: `@Value("${server.port}")` or `@Value("#{someBean.someProperty}")`.

**Q15. What is component scanning, and how is it configured?**
The process by which Spring automatically discovers and registers classes annotated with stereotype annotations (`@Component`, `@Service`, etc.) as beans, without explicit XML/Java configuration for each one. Enabled via `@ComponentScan` (specifying base packages) on a `@Configuration` class, or implicitly by `@SpringBootApplication` (which scans the package of the main class and its sub-packages).

---

## 3. Bean Lifecycle & Scopes

**Q16. What are the phases of the Spring Bean lifecycle?**
1. Instantiation (constructor called).
2. Populate properties (dependency injection).
3. `Aware` interfaces invoked if implemented (`BeanNameAware`, `ApplicationContextAware`, etc.).
4. `BeanPostProcessor.postProcessBeforeInitialization()`.
5. `@PostConstruct` method / `InitializingBean.afterPropertiesSet()` / custom `init-method`.
6. `BeanPostProcessor.postProcessAfterInitialization()` (this is also where proxies, e.g., for AOP, are typically created).
7. Bean is ready for use.
8. On container shutdown: `@PreDestroy` / `DisposableBean.destroy()` / custom `destroy-method`.

**Q17. What is the difference between `@PostConstruct`/`@PreDestroy` and `InitializingBean`/`DisposableBean`?**
`@PostConstruct`/`@PreDestroy` (standard `jakarta.annotation` annotations) mark lifecycle callback methods without coupling the class to Spring-specific interfaces — the preferred, framework-agnostic approach. `InitializingBean.afterPropertiesSet()`/`DisposableBean.destroy()` are Spring-specific interfaces achieving the same purpose, but tightly couple the class to Spring APIs.

**Q18. What is a `BeanPostProcessor`?**
An extension point allowing custom logic to run before and after the initialization of **every** bean in the container (e.g., wrapping beans in proxies, validating properties, modifying bean instances). Spring itself uses `BeanPostProcessor`s internally for features like `@Autowired` processing and AOP proxy creation.

**Q19. What is a `BeanFactoryPostProcessor`, and how does it differ from a `BeanPostProcessor`?**
A `BeanFactoryPostProcessor` operates on **bean definitions** (metadata) before any beans are actually instantiated — e.g., `PropertySourcesPlaceholderConfigurer` (which resolves `${...}` placeholders) modifies bean definition metadata. A `BeanPostProcessor` operates on actual **bean instances** after they've been created, during the initialization phase.

**Q20. What are the Spring bean scopes?**
- **singleton** (default): one shared instance per Spring container.
- **prototype:** a new instance created every time the bean is requested/injected.
- **request:** one instance per HTTP request (web-aware contexts only).
- **session:** one instance per HTTP session (web-aware contexts only).
- **application:** one instance per `ServletContext` (web-aware contexts only).
- **websocket:** one instance per WebSocket session.

**Q21. What is the "singleton" scope in Spring vs. the classic Singleton design pattern?**
Spring's singleton scope means **one instance per Spring IoC container** (not necessarily per JVM) — if multiple Spring contexts exist within the same JVM, each could have its own "singleton" instance of the bean. The classic GoF Singleton pattern typically enforces exactly one instance per JVM/classloader via private constructors and static access, which is a stricter guarantee.

**Q22. How do you inject a prototype-scoped bean into a singleton-scoped bean correctly?**
Simply autowiring a prototype bean into a singleton results in the prototype instance being created **only once** (at singleton wiring time) and reused thereafter — defeating the purpose. Solutions: use a `ObjectFactory<T>`/`Provider<T>` to fetch a fresh instance on demand, use method injection (`@Lookup`), or inject the `ApplicationContext` and call `getBean()` explicitly each time a new instance is needed.

**Q23. What is lazy initialization, and how do you enable it?**
By default, singleton beans in `ApplicationContext` are created eagerly at startup. `@Lazy` defers a bean's creation until it's first requested, which can speed up application startup and avoid creating beans that might never be used — but can also delay the discovery of configuration errors until runtime and add slight latency to the first request that triggers creation.

---

## 4. Configuration Approaches

**Q24. What are the different ways to configure Spring beans?**
1. **XML-based configuration** (`applicationContext.xml`) — legacy approach.
2. **Annotation-based configuration** — `@Component`/stereotype annotations + component scanning.
3. **Java-based configuration** — `@Configuration` classes with `@Bean`-annotated methods, providing full programmatic control and type safety.
Modern Spring (especially Spring Boot) predominantly uses annotation + Java-based configuration.

**Q25. What is the difference between `@Component` and `@Bean`?**
`@Component` is a class-level annotation used with component scanning — Spring detects and instantiates the class automatically. `@Bean` is a method-level annotation inside a `@Configuration` class, giving you explicit, programmatic control over how the bean is instantiated (useful for third-party classes you can't annotate directly, or when construction logic requires conditionals).

**Q26. What does `@Configuration` do, and why does it matter that it uses CGLIB proxying?**
Marks a class as a source of bean definitions. Spring proxies `@Configuration` classes (via CGLIB, i.e., subclassing) so that calls between `@Bean` methods **within the same class** return the same singleton instance (rather than creating a new object each time the Java method is called directly) — preserving singleton semantics even when one `@Bean` method calls another.

**Q27. What is `@ComponentScan`, and what does `@SpringBootApplication` include?**
`@ComponentScan` tells Spring which packages to scan for annotated components. `@SpringBootApplication` is a convenience meta-annotation combining `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan` (scanning the package containing the annotated class and its sub-packages by default) — this is why the main application class should typically sit at the root of your package structure.

**Q28. What is Spring Expression Language (SpEL)?**
A powerful expression language for querying/manipulating object graphs at runtime, usable in annotations (`@Value("#{...}")`) and XML configuration — supports method invocation, property access, arithmetic/logical operators, collection selection/projection, and referencing other beans.

**Q29. What is `@Import`, and when would you use it?**
Allows explicitly importing additional `@Configuration` classes into a configuration class, useful for modularizing configuration across multiple classes/libraries without relying purely on component scanning (e.g., importing configuration from a shared library).

---

## 5. Spring AOP

**Q30. What is Aspect-Oriented Programming (AOP), and what problem does it solve?**
A programming paradigm for modularizing **cross-cutting concerns** — behavior that spans multiple classes/layers (logging, security, transaction management, caching) — into separate reusable modules called **aspects**, rather than scattering the same boilerplate code throughout business logic classes.

**Q31. What are the key AOP terminology/concepts?**
- **Aspect:** a module encapsulating a cross-cutting concern (e.g., a `@Aspect`-annotated class).
- **Join point:** a point during program execution where an aspect can be applied (e.g., a method call).
- **Advice:** the action taken by an aspect at a particular join point (the actual code that runs).
- **Pointcut:** a predicate/expression matching join points where advice should apply.
- **Weaving:** the process of linking aspects with target objects to create an advised object.
- **Target object:** the object being advised.

**Q32. What are the types of advice in Spring AOP?**
- `@Before`: runs before the join point.
- `@AfterReturning`: runs after the method successfully returns.
- `@AfterThrowing`: runs if the method throws an exception.
- `@After` (finally): runs regardless of outcome (success or exception).
- `@Around`: wraps the join point entirely — can control whether the method executes at all, modify arguments/return values, and measure execution time; must explicitly call `joinPoint.proceed()`.

**Q33. How does Spring AOP implement proxying, and what is the difference between JDK dynamic proxies and CGLIB proxies?**
Spring AOP creates a **proxy** object wrapping the target bean to intercept method calls. **JDK dynamic proxies** are used when the target class implements at least one interface (proxy implements the same interface(s)) — pure Java, no extra dependency. **CGLIB proxies** are used when the target class doesn't implement an interface — generated via runtime subclassing of the target class (so the target class/methods can't be `final`).

**Q34. What is the limitation of Spring AOP regarding self-invocation?**
If a method within a bean calls another method **on the same bean instance** internally (`this.otherMethod()`), the call bypasses the proxy entirely — so any AOP advice (e.g., `@Transactional`, `@Cacheable`) on the internally-called method **will not be applied**. This is because the proxy only intercepts calls coming from *outside* the object. Workarounds: refactor the internal call into a separate bean, or use `AopContext.currentProxy()` (requires exposing the proxy).

**Q35. What is the difference between Spring AOP and AspectJ?**
**Spring AOP** is proxy-based, works only on Spring-managed beans, and only supports method-execution join points — simpler to use, sufficient for most application-level cross-cutting concerns. **AspectJ** is a full-featured, more powerful AOP implementation using compile-time or load-time bytecode weaving, supporting field access, constructor, and many more join point types beyond method execution — more powerful but more complex to set up; Spring can integrate with AspectJ for advanced use cases.

---

## 6. Spring Boot

**Q36. What is Spring Boot, and how does it differ from the Spring Framework?**
Spring Boot is a project built on top of the Spring Framework that simplifies application setup and development by providing **auto-configuration**, an **embedded server** (Tomcat/Jetty/Undertow — no need to deploy a WAR to an external server), **starter dependencies** (curated dependency bundles), and production-ready features (Actuator) — dramatically reducing boilerplate configuration compared to traditional Spring applications.

**Q37. What is auto-configuration, and how does it work?**
Spring Boot automatically configures beans based on the dependencies present on the classpath and existing bean definitions, using conditional annotations (`@ConditionalOnClass`, `@ConditionalOnMissingBean`, `@ConditionalOnProperty`) within `@AutoConfiguration` classes (registered via `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` in modern Spring Boot, or `spring.factories` historically). E.g., if `spring-boot-starter-data-jpa` and an embedded H2 driver are on the classpath, Spring Boot auto-configures a `DataSource`, `EntityManagerFactory`, and `TransactionManager` automatically.

**Q38. What are Spring Boot starters?**
Convenience dependency descriptors that bundle a set of related, compatible dependencies together — e.g., `spring-boot-starter-web` pulls in Spring MVC, an embedded Tomcat, Jackson (JSON), and validation libraries. Simplifies dependency management and avoids version-compatibility issues.

**Q39. What is `@SpringBootApplication`, and what three annotations does it combine?**
A meta-annotation combining: `@Configuration` (marks the class as a configuration source), `@EnableAutoConfiguration` (triggers Spring Boot's auto-configuration mechanism), and `@ComponentScan` (enables component scanning from the class's package).

**Q40. How do you override/customize Spring Boot's auto-configuration?**
Define your own bean of the relevant type — most auto-configurations use `@ConditionalOnMissingBean`, so your explicit bean definition takes precedence and the auto-configured default backs off. You can also selectively exclude specific auto-configuration classes: `@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)`, or configure via `application.properties`/`application.yml`.

**Q41. What is the difference between `application.properties` and `application.yml`?**
Both configure application settings, but `.yml` uses YAML's hierarchical, indentation-based structure (more concise for nested properties, supports lists naturally), while `.properties` uses flat key-value pairs with dot notation. Functionally equivalent — choice is largely a matter of team preference/readability.

**Q42. What embedded servers does Spring Boot support, and how do you switch between them?**
Tomcat (default with `spring-boot-starter-web`), Jetty, and Undertow. Switch by excluding the default Tomcat starter and including the desired one: 
```xml
<exclusions><exclusion><groupId>org.springframework.boot</groupId><artifactId>spring-boot-starter-tomcat</artifactId></exclusion></exclusions>
```
then add `spring-boot-starter-jetty` (or Undertow).

**Q43. How do you build an executable JAR with Spring Boot, and how does it differ from a traditional WAR?**
The Spring Boot Maven/Gradle plugin packages the application, its dependencies, and an embedded server into a single **fat/executable JAR** (`java -jar app.jar` runs it directly) using a nested-JAR class-loading mechanism — unlike a traditional WAR, which requires deployment to an external servlet container (e.g., a standalone Tomcat instance).

**Q44. What is `CommandLineRunner`/`ApplicationRunner`?**
Functional interfaces you can implement (as a bean) to run code immediately after the Spring Boot application context has fully started — useful for startup tasks like seeding data or validating configuration. `ApplicationRunner` provides parsed `ApplicationArguments`, while `CommandLineRunner` provides raw `String[] args`.

**Q45. What is a `spring.factories` file / auto-configuration imports file used for?**
The mechanism (legacy: `META-INF/spring.factories`; modern: `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`) by which Spring Boot discovers auto-configuration classes to load at startup — this is how third-party libraries can provide their own Spring Boot auto-configuration ("starters") that activate automatically when included as a dependency.

---

## 7. Spring MVC

**Q46. What is the front controller pattern, and how does `DispatcherServlet` implement it?**
The front controller pattern centralizes request handling through a single entry point that delegates to appropriate handlers, rather than having many separate servlets. Spring MVC's `DispatcherServlet` is this central entry point — it receives all incoming HTTP requests, consults a `HandlerMapping` to find the appropriate controller method, invokes it, and then resolves the returned result into an HTTP response (via a `ViewResolver` for traditional MVC, or directly serializing the object for REST via `HttpMessageConverter`).

**Q47. What is the request processing flow in Spring MVC?**
1. `DispatcherServlet` receives the HTTP request.
2. Consults `HandlerMapping` to determine which controller method should handle it.
3. Invokes the controller method (potentially through `HandlerAdapter`, handling argument resolution/binding).
4. The controller processes the request and returns a result (a `ModelAndView`, a `String` view name, or a domain object for REST).
5. For traditional MVC: `ViewResolver` resolves the logical view name to an actual view (e.g., a JSP/Thymeleaf template), which is rendered with the model data.
6. For REST: an `HttpMessageConverter` (e.g., Jackson) serializes the returned object directly to the response body (JSON/XML).

**Q48. What is the difference between `@Controller` and `@RestController`?**
`@Controller` is used for traditional MVC where methods typically return a **view name** (String) to be resolved and rendered as HTML. `@RestController` = `@Controller` + `@ResponseBody` applied to every method — methods return domain objects/DTOs directly, which are automatically serialized (typically to JSON) and written to the response body, appropriate for REST APIs.

**Q49. What are common request-mapping annotations?**
`@RequestMapping` (general-purpose, configurable HTTP method), and its method-specific shortcuts: `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`, `@PatchMapping`.

**Q50. What are common parameter-binding annotations in Spring MVC?**
- `@PathVariable`: extracts a value from the URI path (`/users/{id}`).
- `@RequestParam`: extracts a query parameter or form field.
- `@RequestBody`: deserializes the HTTP request body (e.g., JSON) into a Java object.
- `@RequestHeader`: extracts an HTTP header value.
- `@ModelAttribute`: binds form data (or query params) to a Java object; also usable to add attributes to the model.
- `@CookieValue`: extracts a value from a cookie.

**Q51. What is `@ResponseBody`, and how does content negotiation work?**
`@ResponseBody` tells Spring to write the return value directly to the HTTP response body (via an `HttpMessageConverter`) instead of resolving it as a view name. **Content negotiation** determines the response format (JSON, XML, etc.) based on the request's `Accept` header, URL suffix, or a configured default — Spring selects the appropriate `HttpMessageConverter` accordingly (Jackson for JSON is the default/most common).

**Q52. What is a `ModelAndView`, and when would you use it vs. returning a plain object?**
`ModelAndView` bundles both the data (`Model`) and the logical view name together, used in traditional (non-REST) Spring MVC controllers rendering server-side views (JSP, Thymeleaf). For REST APIs (`@RestController`), you typically just return the domain object/DTO directly, since there's no view to render — it's serialized straight to the response body.

**Q53. What are Spring MVC interceptors, and how do they differ from filters?**
A `HandlerInterceptor` (`preHandle`, `postHandle`, `afterCompletion`) operates within the Spring MVC framework, with access to the resolved handler (controller method) and Spring-specific context — used for cross-cutting concerns like logging, authentication checks tied to Spring's dispatch mechanism. A **Servlet Filter** operates at the raw servlet container level, before requests even reach `DispatcherServlet`, with no knowledge of Spring MVC internals — used for more generic, framework-agnostic concerns (e.g., CORS, raw request/response manipulation, encoding).

---

## 8. REST API Development

**Q54. What HTTP status codes are commonly used in REST API design, and what do they mean?**
- `200 OK`: successful GET/PUT/PATCH.
- `201 Created`: successful POST creating a resource.
- `204 No Content`: successful request with no response body (e.g., DELETE).
- `400 Bad Request`: invalid request (validation failure).
- `401 Unauthorized`: authentication required/failed.
- `403 Forbidden`: authenticated but lacking permission.
- `404 Not Found`: resource doesn't exist.
- `409 Conflict`: request conflicts with current resource state.
- `500 Internal Server Error`: unhandled server-side error.

**Q55. How do you validate request bodies in Spring MVC?**
Use Bean Validation (JSR 380/Jakarta Validation) annotations on the DTO (`@NotNull`, `@Size`, `@Email`, `@Min`/`@Max`, custom validators), then annotate the controller method parameter with `@Valid` or `@Validated`. If validation fails, Spring throws `MethodArgumentNotValidException`, typically handled via a `@ControllerAdvice` to return a structured `400 Bad Request` error response.

**Q56. What is HATEOAS, and does Spring support it?**
Hypermedia As The Engine Of Application State — a REST maturity principle where responses include links to related actions/resources, allowing clients to navigate the API dynamically rather than hardcoding URLs. Spring provides **Spring HATEOAS**, a library for building hypermedia-driven responses (`EntityModel`, `Link`, `WebMvcLinkBuilder`).

**Q57. How do you version a REST API in Spring?**
Common strategies: **URI versioning** (`/api/v1/users`), **request parameter versioning** (`/api/users?version=1`), **header versioning** (custom header like `X-API-Version`), or **media-type/content-negotiation versioning** (`Accept: application/vnd.company.app-v1+json`). Spring doesn't enforce one approach — it's an application design decision, with URI versioning being the simplest/most common for public APIs.

**Q58. How do you enable CORS in Spring?**
At the method/class level: `@CrossOrigin(origins = "https://example.com")`. Globally, via a `WebMvcConfigurer` bean overriding `addCorsMappings()`, or via Spring Security's CORS configuration if Security is in use (Security's filter runs before MVC's, so CORS must be configured there too in secured applications).

---

## 9. Spring Data JPA

**Q59. What is Spring Data JPA, and what problem does it solve?**
An abstraction layer over JPA (Java Persistence API, typically implemented by Hibernate) that dramatically reduces boilerplate data-access code by generating repository implementations automatically from interface definitions — eliminating the need to hand-write most CRUD and simple query logic.

**Q60. What is the `JpaRepository` interface hierarchy?**
`Repository` (marker interface) → `CrudRepository` (basic CRUD operations) → `PagingAndSortingRepository` (adds pagination/sorting) → `JpaRepository` (adds JPA-specific batch operations, flushing). Extending `JpaRepository<Entity, IdType>` gives you a full-featured repository with no implementation code required.

**Q61. How does Spring Data JPA generate queries from method names?**
Via **query derivation** — Spring parses the method name following naming conventions and builds the corresponding query automatically, e.g., `findByLastNameAndAgeGreaterThan(String lastName, int age)` generates `WHERE last_name = ? AND age > ?`. Supports keywords like `findBy`, `countBy`, `existsBy`, `deleteBy`, combined with `And`, `Or`, `Between`, `LessThan`, `Like`, `OrderBy`, etc.

**Q62. What is the difference between `@Query` with JPQL vs. native SQL?**
`@Query("SELECT u FROM User u WHERE u.email = :email")` uses **JPQL** (Java Persistence Query Language) — operates on entity objects/fields (not table/column names directly), and is database-agnostic. `@Query(value = "SELECT * FROM users WHERE email = :email", nativeQuery = true)` uses raw **native SQL**, giving full access to database-specific features but sacrificing portability across different databases.

**Q63. What is the N+1 select problem, and how do you avoid it?**
Occurs when fetching a list of parent entities triggers one query, but then each parent's lazily-loaded association triggers a **separate** query to fetch its related data — resulting in `1 + N` queries instead of one efficient query. Solutions: use `JOIN FETCH` in JPQL, `@EntityGraph` to specify eager loading for a specific query, or batch fetching configuration (`hibernate.default_batch_fetch_size`).

**Q64. What is the difference between `FetchType.EAGER` and `FetchType.LAZY`?**
`EAGER` loads the associated entity/collection immediately along with the parent entity. `LAZY` defers loading the association until it's actually accessed (via a proxy) — generally preferred as the default for collections/associations to avoid loading unnecessary data, but can cause `LazyInitializationException` if accessed outside an active persistence context (e.g., after the transaction/session has closed).

**Q65. What is `LazyInitializationException`, and how do you fix it?**
Thrown when code tries to access a lazily-loaded association **after** the persistence context (Hibernate session) that loaded the parent entity has already closed (e.g., in a view layer after the `@Transactional` service method has returned). Fixes: fetch the needed data explicitly within the transactional method (e.g., via `JOIN FETCH` or accessing it before returning), use DTOs projected within the transaction, or (less recommended) enable "Open Session in View" (which has its own trade-offs regarding connection holding).

**Q66. What is the difference between `save()` and `saveAndFlush()`?**
`save()` persists the entity but may defer the actual SQL `INSERT`/`UPDATE` until the persistence context is flushed (e.g., at transaction commit, or automatically before certain queries). `saveAndFlush()` immediately flushes the change to the database, useful when you need the change to be visible to subsequent operations within the same transaction (e.g., a native query that wouldn't otherwise see the pending change).

**Q67. What are JPA entity states (lifecycle)?**
- **Transient:** a new object, not yet associated with a persistence context or database row.
- **Managed (Persistent):** associated with an active persistence context; changes are automatically tracked and synced to the database (dirty checking).
- **Detached:** was previously managed, but the persistence context has closed (or the entity was explicitly detached) — changes are no longer tracked.
- **Removed:** marked for deletion within the persistence context, will be deleted upon flush/commit.

**Q68. What is dirty checking in Hibernate/JPA?**
Within an active transaction, Hibernate automatically tracks changes made to managed entities' fields and generates the corresponding `UPDATE` SQL statement automatically at flush/commit time — **without** requiring an explicit `save()`/`update()` call for already-managed entities.

**Q69. How do you implement pagination and sorting with Spring Data JPA?**
```java
Pageable pageable = PageRequest.of(0, 20, Sort.by("lastName").ascending());
Page<User> page = userRepository.findAll(pageable);
```
`Pageable` encapsulates page number, size, and sort order; the repository method returns a `Page<T>` (includes total count/pages) or `Slice<T>` (doesn't compute total count, cheaper for infinite-scroll-style UIs).

**Q70. What is `@EntityGraph`, and when is it useful?**
Allows specifying, at the repository method level, which associations should be eagerly fetched for that specific query — without changing the entity's default `FetchType`, giving fine-grained, per-query control over fetching strategy (a common solution to the N+1 problem for specific use cases).

---

## 10. Transaction Management

**Q71. How does Spring's declarative transaction management work?**
Via the `@Transactional` annotation, which Spring implements using **AOP proxying** — the framework wraps the annotated bean in a proxy that begins a transaction before the method executes and commits (or rolls back, on exception) after it completes, without requiring manual transaction boilerplate in the business logic.

**Q72. What causes `@Transactional` to not work as expected (the self-invocation problem)?**
Since `@Transactional` relies on AOP proxying, calling a `@Transactional` method **from within the same class** (`this.transactionalMethod()`) bypasses the proxy — the transactional behavior won't apply, since the call never goes through the intercepting proxy. Also fails on **private methods** (proxies can't override/intercept private methods) and if the class itself isn't Spring-managed.

**Q73. What are the propagation types for `@Transactional`?**
- `REQUIRED` (default): joins an existing transaction if one exists, or creates a new one if not.
- `REQUIRES_NEW`: always creates a new, independent transaction, suspending any existing one.
- `NESTED`: creates a nested transaction (savepoint) within an existing one, which can be rolled back independently without affecting the outer transaction.
- `SUPPORTS`: joins a transaction if one exists, otherwise runs non-transactionally.
- `NOT_SUPPORTED`: suspends any existing transaction, runs non-transactionally.
- `MANDATORY`: requires an existing transaction; throws an exception if none exists.
- `NEVER`: throws an exception if a transaction exists.

**Q74. What are the isolation levels supported by `@Transactional`?**
`DEFAULT` (use the underlying database's default), plus the standard SQL levels: `READ_UNCOMMITTED`, `READ_COMMITTED`, `REPEATABLE_READ`, `SERIALIZABLE` (see SQL isolation levels for their precise meaning).

**Q75. By default, does `@Transactional` roll back on checked or unchecked exceptions?**
By default, Spring rolls back only on **unchecked exceptions** (`RuntimeException` and `Error`), **not** on checked exceptions. To roll back on a checked exception, you must explicitly configure it: `@Transactional(rollbackFor = MyCheckedException.class)`.

**Q76. What is the difference between programmatic and declarative transaction management?**
**Declarative** (`@Transactional`) uses annotations/AOP to manage transactions without explicit code — the standard, preferred approach. **Programmatic** transaction management uses `TransactionTemplate` or `PlatformTransactionManager` directly in code, giving finer-grained control (e.g., managing transaction boundaries dynamically based on runtime logic) at the cost of more boilerplate.

**Q77. What is `readOnly = true` on `@Transactional` used for?**
A hint to the underlying persistence provider/database that the transaction won't perform any writes, allowing potential optimizations (e.g., Hibernate can skip dirty-checking overhead for read-only sessions, and some databases can apply read-only optimizations at the connection level) — purely a performance optimization, not an enforced constraint by Spring itself.

---

## 11. Spring Security

**Q78. What is the core architecture of Spring Security?**
Built around a chain of **Servlet Filters** (the `FilterChainProxy`/`SecurityFilterChain`) that intercept every incoming request before it reaches the application, performing authentication and authorization checks. Key components: `AuthenticationManager` (verifies credentials), `UserDetailsService` (loads user-specific data), `SecurityContext`/`SecurityContextHolder` (stores the currently authenticated principal, typically thread-local), and `AccessDecisionManager`/`AuthorizationManager` (determines whether access is permitted).

**Q79. What is the difference between authentication and authorization?**
**Authentication** verifies *who* the user is (validating credentials — e.g., username/password, token). **Authorization** determines *what* an authenticated user is allowed to do (permissions, roles, access to specific resources) — authentication always happens first, authorization decisions are based on the authenticated identity.

**Q80. How do you configure security rules in modern Spring Security (Spring Boot 3+)?**
Via a `SecurityFilterChain` bean (the `WebSecurityConfigurerAdapter` class-based approach was deprecated/removed in Spring Security 5.7+/6):
```java
@Bean
SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http.authorizeHttpRequests(auth -> auth
            .requestMatchers("/public/**").permitAll()
            .requestMatchers("/admin/**").hasRole("ADMIN")
            .anyRequest().authenticated())
        .formLogin(Customizer.withDefaults());
    return http.build();
}
```

**Q81. What is `UserDetailsService`?**
An interface with a single method, `loadUserByUsername(String username)`, that Spring Security calls to retrieve user-specific data (username, password hash, authorities/roles) during authentication — typically implemented to fetch user data from a database.

**Q82. How does password encoding work in Spring Security?**
Passwords should never be stored in plain text. Spring Security provides `PasswordEncoder` implementations (most commonly `BCryptPasswordEncoder`, using the adaptive, salted BCrypt hashing algorithm) — passwords are hashed before storage, and login verification compares the hash of the submitted password against the stored hash (never decrypting/comparing plain text).

**Q83. What is CSRF, and how does Spring Security protect against it?**
**Cross-Site Request Forgery** — an attack tricking an authenticated user's browser into submitting an unwanted request to an application they're logged into. Spring Security protects against it by default for state-changing requests (POST/PUT/DELETE) by requiring a unique, unpredictable **CSRF token** to be included in the request, which the server validates — typically disabled for stateless REST APIs using token-based auth (JWT), since CSRF primarily concerns cookie-based session authentication.

**Q84. What is the difference between session-based and token-based (JWT) authentication?**
**Session-based:** the server creates a session upon login, stores session state server-side, and issues a session ID (typically via cookie) that the client sends on subsequent requests — server must maintain session state (or share it across instances). **Token-based (JWT):** the server issues a signed, self-contained token (JSON Web Token) containing claims (user identity, roles, expiration) upon login; the client sends this token (typically in the `Authorization: Bearer` header) on each request, and the server validates the signature **without needing to store any session state** — enabling stateless, horizontally scalable authentication, common in microservices/REST APIs.

**Q85. What is method-level security, and how do you enable it?**
Allows applying authorization rules directly on service/controller methods via annotations: `@PreAuthorize("hasRole('ADMIN')")`, `@PostAuthorize`, `@Secured("ROLE_ADMIN")`. Enabled via `@EnableMethodSecurity` on a configuration class.

**Q86. What is OAuth2, and how does Spring Security support it?**
OAuth2 is an authorization framework allowing third-party applications to obtain limited access to a user's resources without exposing credentials, typically involving an authorization server issuing access tokens. Spring Security provides `spring-security-oauth2-client` (for consuming OAuth2/OIDC providers like Google/GitHub login) and `spring-security-oauth2-resource-server` (for validating incoming bearer tokens/JWTs in a resource server/API).

---

## 12. Exception Handling

**Q87. How do you handle exceptions globally in a Spring MVC/REST application?**
Using `@ControllerAdvice` (or `@RestControllerAdvice` for REST APIs) combined with `@ExceptionHandler` methods — centralizes exception-to-HTTP-response mapping logic across all controllers, rather than duplicating try-catch blocks in every controller method:
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(new ErrorResponse(ex.getMessage()));
    }
}
```

**Q88. What is `ResponseEntity`, and why use it instead of returning a plain object?**
A wrapper representing the full HTTP response — including status code, headers, and body — giving controllers explicit control over the response beyond just the body (e.g., `ResponseEntity.status(HttpStatus.CREATED).header("Location", uri).body(dto)`), whereas returning a plain object always defaults to `200 OK`.

**Q89. What is `@ResponseStatus`?**
An annotation that can be applied to a custom exception class (or an `@ExceptionHandler` method) to declaratively specify the HTTP status code that should be returned when that exception is thrown/handled, without manually building a `ResponseEntity`.

---

## 13. Spring Testing

**Q90. What is `@SpringBootTest`, and when should you use it vs. slice testing annotations?**
`@SpringBootTest` loads the **full application context** for integration testing — comprehensive but slower (more beans to initialize). "Slice" test annotations load only a relevant subset of the context for faster, more focused tests: `@WebMvcTest` (loads only web/MVC layer components, mocking the service layer), `@DataJpaTest` (loads only JPA/repository components, often against an in-memory database), `@JsonTest`, etc.

**Q91. What is the difference between `@Mock`, `@MockBean`, and `@Spy`?**
`@Mock` (Mockito) creates a mock object for use in plain unit tests (no Spring context involved). `@MockBean` (Spring Boot Test) creates a Mockito mock **and registers it in the Spring application context**, replacing any existing bean of that type — used in Spring context-based integration tests to isolate the component under test from real dependencies. `@Spy` wraps a real object, allowing selective method overriding while other methods execute normally.

**Q92. What is `TestRestTemplate`/`WebTestClient`, and when would you use each?**
Both are used for integration testing actual HTTP endpoints (as opposed to calling controller methods directly). `TestRestTemplate` is the traditional, blocking client, typically used with `@SpringBootTest(webEnvironment = RANDOM_PORT)` for Spring MVC applications. `WebTestClient` is the reactive equivalent, used for testing Spring WebFlux applications (or also usable for MVC apps), supporting a fluent, non-blocking assertion API.

**Q93. What is `@Transactional` used for in tests, and what's the default rollback behavior?**
Applying `@Transactional` to a test method wraps it in a transaction that is, by default, **automatically rolled back** at the end of the test — keeping the database clean between tests without needing manual cleanup, useful for integration tests that touch a real/embedded database.

**Q94. What is Testcontainers, and why is it commonly used with Spring Boot tests?**
A library that lets tests spin up real, disposable Docker containers (e.g., an actual PostgreSQL instance, Kafka broker) programmatically for integration testing — providing much higher fidelity than in-memory substitutes (like H2 standing in for PostgreSQL), catching database-specific behavior/SQL dialect issues that an in-memory database might mask.

---

## 14. Spring Boot Actuator & Observability

**Q95. What is Spring Boot Actuator?**
A sub-project providing production-ready monitoring and management features out of the box — exposing endpoints for health checks, metrics, application info, environment properties, thread dumps, and more, typically over HTTP (and/or JMX).

**Q96. What are some commonly used Actuator endpoints?**
`/actuator/health` (application health status, including custom health indicators), `/actuator/metrics` (application metrics — memory, HTTP request counts/latencies, etc.), `/actuator/info` (build/app info), `/actuator/env` (environment properties), `/actuator/loggers` (view/modify logging levels at runtime), `/actuator/beans` (list of all Spring beans).

**Q97. How do you secure Actuator endpoints in production?**
Since some endpoints expose sensitive information (environment variables, bean configuration), best practices include: only exposing necessary endpoints (`management.endpoints.web.exposure.include=health,info`), securing them behind Spring Security rules (restricting access to authenticated/admin users), and/or running them on a separate management port not exposed publicly.

**Q98. What is Micrometer, and how does it relate to Actuator?**
A vendor-neutral application metrics facade (similar in spirit to SLF4J for logging) that Spring Boot Actuator uses internally to collect and expose metrics — supporting multiple monitoring system backends (Prometheus, Datadog, New Relic, etc.) through a consistent API, so you can switch monitoring vendors without changing instrumentation code.

---

## 15. Microservices & Spring Cloud

**Q99. What is Spring Cloud, and what problems does it address?**
A collection of tools built on Spring Boot for building distributed systems/microservices, addressing common concerns: service discovery, centralized configuration, load balancing, circuit breaking/resilience, distributed tracing, and API gateways.

**Q100. What is service discovery, and how does it work (e.g., with Eureka)?**
Allows services in a distributed system to dynamically find and communicate with each other without hardcoded network locations. Services register themselves with a **discovery server** (e.g., Netflix Eureka, Consul) at startup, and other services query the discovery server to find current instances/addresses of a needed service — essential in environments where instances scale up/down dynamically and IPs change frequently.

**Q101. What is Spring Cloud Config, and why is centralized configuration important in microservices?**
A server that centralizes externalized configuration for multiple microservices/environments (often backed by a Git repository), allowing configuration to be managed and updated independently of application deployment — important because microservices architectures involve many independently deployable services, each needing consistent, environment-specific configuration without rebuilding/redeploying.

**Q102. What is a circuit breaker pattern, and how is it implemented in Spring (e.g., Resilience4j)?**
A resilience pattern that prevents a failing downstream service from cascading failures throughout the system — after a threshold of failures, the circuit "opens" and subsequent calls fail fast (or fall back to a default behavior) without attempting the actual call, giving the failing service time to recover; after a cooldown, it periodically allows a few test calls through ("half-open") to check recovery. Spring Cloud Circuit Breaker (commonly backed by **Resilience4j**, since Netflix Hystrix is now in maintenance mode) provides annotations (`@CircuitBreaker`) for this.

**Q103. What is an API Gateway, and what role does Spring Cloud Gateway play?**
A single entry point that sits in front of multiple microservices, handling cross-cutting concerns like routing, authentication, rate limiting, and request/response transformation — so individual services don't need to duplicate this logic. **Spring Cloud Gateway** is Spring's reactive, non-blocking API gateway implementation, built on Spring WebFlux.

**Q104. What is distributed tracing, and why is it needed in microservices?**
The practice of tracking a single logical request as it flows across multiple microservices, correlating logs/spans from each service involved using a shared trace ID — essential for debugging latency issues and failures in a distributed system where a single user request might touch dozens of services. Spring Boot 3+ uses **Micrometer Tracing** (successor to Spring Cloud Sleuth) integrated with backends like Zipkin or OpenTelemetry.

**Q105. What is client-side vs. server-side load balancing, and how does Spring support it?**
**Server-side** load balancing routes requests through a centralized load balancer (e.g., a hardware/cloud LB, or Nginx) that distributes traffic to backend instances. **Client-side** load balancing (e.g., via Spring Cloud LoadBalancer, replacing the now-retired Netflix Ribbon) lets the calling service itself choose which instance to call, typically using service discovery data — avoiding a centralized bottleneck/single point of failure.

---

## 16. Reactive Programming (Spring WebFlux)

**Q106. What is Spring WebFlux, and how does it differ from Spring MVC?**
Spring WebFlux is Spring's **reactive**, non-blocking web framework, built on Project Reactor and running on non-blocking servers (Netty by default, or Servlet 3.1+ containers in non-blocking mode). Unlike Spring MVC's traditional **thread-per-request, blocking** model, WebFlux uses a small, fixed number of event-loop threads that never block waiting on I/O — enabling much higher concurrency/throughput for I/O-bound workloads (many concurrent connections) with fewer threads/less memory overhead.

**Q107. What are `Mono` and `Flux`?**
The two core reactive types from Project Reactor, both implementing the Reactive Streams `Publisher` interface. `Mono<T>` represents an asynchronous stream of **0 or 1** element. `Flux<T>` represents an asynchronous stream of **0 to N** elements — both are lazy (nothing happens until subscribed to) and support a rich set of composable operators (`map`, `flatMap`, `filter`, `zip`, etc.).

**Q108. When should you choose WebFlux over traditional Spring MVC?**
WebFlux shines for **I/O-bound** workloads with high concurrency requirements (many simultaneous slow/streaming connections, e.g., high-throughput APIs, streaming data, WebSockets) where the non-blocking model's efficiency pays off. For typical CRUD applications with blocking dependencies (e.g., a JDBC-based relational database, which is inherently blocking), Spring MVC is often simpler and just as effective — mixing blocking calls into a reactive pipeline defeats WebFlux's benefits and can actually hurt performance (blocking an event-loop thread).

**Q109. What is backpressure in reactive streams?**
A mechanism allowing a subscriber (consumer) to signal to the publisher (producer) how much data it's ready to receive, preventing a fast producer from overwhelming a slower consumer's ability to process data — a core feature of the Reactive Streams specification that Project Reactor implements.

**Q110. What is the difference between `subscribeOn()` and `publishOn()` in Project Reactor?**
`subscribeOn()` controls which thread/scheduler the **subscription and upstream source emission** occurs on — affects the entire chain regardless of where it's placed. `publishOn()` switches the execution context for **downstream operators** from that point onward in the chain — allowing fine-grained control over which parts of a reactive pipeline run on which thread pool (e.g., offloading a blocking call to a dedicated scheduler).

---

## 17. Messaging & Integration

**Q111. How does Spring integrate with message brokers like RabbitMQ and Kafka?**
**Spring AMQP** (`spring-boot-starter-amqp`) provides `RabbitTemplate` for sending messages and `@RabbitListener` for consuming them, integrating with RabbitMQ. **Spring for Apache Kafka** (`spring-kafka`) provides `KafkaTemplate` for producing messages and `@KafkaListener` for consuming them — both abstract away much of the boilerplate connection/serialization handling of the native client libraries.

**Q112. What is the difference between a message queue (like RabbitMQ) and an event streaming platform (like Kafka) at a conceptual level?**
A traditional **message queue** (RabbitMQ) typically delivers each message to one consumer and removes it once acknowledged — modeled around point-to-point/pub-sub messaging with a focus on reliable delivery and complex routing (exchanges/queues/bindings). An **event streaming platform** (Kafka) retains messages in an ordered, durable, replayable log for a configured retention period, allowing multiple independent consumer groups to read the same stream at their own pace and replay historical events — better suited for event sourcing, stream processing, and high-throughput log-based architectures.

**Q113. What is `@JmsListener`/`@RabbitListener`/`@KafkaListener` used for?**
Annotation-driven message consumption — marking a method to automatically receive and process messages from a specified destination (queue/topic), with Spring handling the underlying listener container, connection management, and message deserialization.

---

## 18. Caching

**Q114. How does Spring's caching abstraction work?**
Spring provides a cache abstraction (`@EnableCaching`, `@Cacheable`, `@CachePut`, `@CacheEvict`) that works similarly to `@Transactional` — via AOP proxying, intercepting annotated method calls to check/populate/evict a cache before/after the actual method executes, decoupling caching logic from business logic. The actual cache implementation (Caffeine, Redis, EhCache, simple in-memory `ConcurrentHashMap`) is pluggable via a `CacheManager`.

**Q115. What is the difference between `@Cacheable`, `@CachePut`, and `@CacheEvict`?**
- `@Cacheable`: checks the cache first; if a value exists for the computed key, returns it without executing the method body; otherwise executes the method and caches the result.
- `@CachePut`: **always** executes the method, then updates the cache with the result — used when you need to guarantee the method runs (e.g., an update operation) while also refreshing the cache.
- `@CacheEvict`: removes an entry (or clears the entire cache) — used typically on delete/update operations to invalidate stale cached data.

**Q116. What is the same self-invocation limitation that applies to caching annotations, as it does to `@Transactional`?**
Since caching (like `@Transactional`) relies on AOP proxies, calling a `@Cacheable`-annotated method from within the same class bypasses the proxy, so the caching behavior won't be applied — the same underlying limitation discussed in Q34/Q72.

---

## 19. Profiles, Properties & Environment

**Q117. What are Spring Profiles, and how do you use them?**
A mechanism for defining environment-specific configuration/beans (e.g., `dev`, `test`, `prod`), activated via the `spring.profiles.active` property. Beans/configuration classes can be scoped to a specific profile using `@Profile("dev")`, and profile-specific property files (`application-dev.yml`) override/supplement the base `application.yml`.

**Q118. What is the property resolution/override precedence in Spring Boot?**
Spring Boot supports configuration from many sources, resolved in a defined precedence order (highest overriding lowest), roughly: command-line arguments > environment variables > `application-{profile}.yml`/`.properties` > `application.yml`/`.properties` > `@PropertySource` annotations > default properties. This flexible layering allows overriding configuration per environment without modifying packaged application code.

**Q119. What is `@ConfigurationProperties`, and how does it differ from `@Value`?**
`@ConfigurationProperties(prefix = "app")` binds a whole group of related, hierarchical properties directly onto a strongly-typed POJO (including nested objects and lists) in one step — more maintainable and type-safe than injecting each property individually with `@Value`, especially for larger configuration blocks. `@Value` is better suited for injecting single, simple values.

**Q120. What is the `Environment` abstraction in Spring?**
Represents the environment the application is running in, providing unified access to both **profiles** (which are active) and **properties** (from all configured property sources — system properties, environment variables, property files) — the underlying mechanism that powers `@Value` and `@ConfigurationProperties` resolution.

---

## 20. Best Practices & Common Pitfalls

**Q121. Why is constructor injection generally preferred over field injection?**
Constructor injection makes dependencies explicit and immutable (`final` fields), fails fast at object-creation time if a required dependency is missing, makes unit testing straightforward (you can instantiate the class directly with mocks, without needing a Spring context or reflection-based field injection), and prevents circular dependency issues from silently working around a design problem.

**Q122. What is the danger of using `@Autowired` field injection extensively in a large codebase?**
Hides a class's true dependencies (not visible from the constructor signature), makes the class harder to unit test without a Spring container (or resorting to reflection-based mocking), can silently mask circular dependency design issues (since Spring can resolve them for field injection), and encourages classes to accumulate too many dependencies unnoticed (a sign the class may be violating single responsibility).

**Q123. Why should you avoid business logic in `@Controller` classes?**
Controllers should be a thin layer responsible for HTTP concerns (request parsing, response formatting, status codes) — business logic belongs in the **service layer**, keeping controllers testable independent of HTTP mechanics, promoting reuse of business logic across different entry points (REST, messaging, scheduled jobs), and maintaining clear separation of concerns.

**Q124. What is the layered architecture typically used in Spring applications?**
**Controller layer** (handles HTTP requests/responses) → **Service layer** (business logic, transaction boundaries) → **Repository/DAO layer** (data access) → **Database**. DTOs (Data Transfer Objects) are commonly used at the controller boundary to decouple the external API contract from internal domain/entity models.

**Q125. Why is it recommended to avoid returning JPA entities directly from REST controllers?**
Directly exposing entities risks leaking internal database structure/lazy-loading issues (serializing an uninitialized lazy association can throw exceptions or trigger unwanted N+1 queries), tightly couples the API contract to the database schema (schema changes become breaking API changes), and can accidentally expose sensitive fields. Using dedicated **DTOs** decouples these concerns and gives explicit control over the API's shape.

**Q126. What are common causes of slow Spring Boot application startup, and how do you address them?**
Excessive component scanning scope, too many eagerly-initialized singleton beans, heavy auto-configuration processing (many starters pulled in unnecessarily), and classpath scanning overhead. Mitigations: use `@Lazy` initialization where appropriate, trim unused starters/dependencies, use Spring's lazy initialization globally (`spring.main.lazy-initialization=true`) for faster (though not necessarily production-recommended) startup, or consider Spring Native/GraalVM for significantly faster cold-start in serverless/CLI contexts.

**Q127. What is a common mistake when using `@Transactional` with `readOnly` and calling write operations?**
Marking a transaction `readOnly = true` and then performing a write operation within it can lead to undefined behavior (some databases/drivers may throw an error, others may silently allow it depending on configuration) — it's a hint/optimization, not a hard guarantee enforced identically everywhere, so it should be used accurately to reflect the method's actual intent.

**Q128. What is the risk of catching and swallowing exceptions broadly in Spring service methods, especially combined with `@Transactional`?**
If an exception is caught internally and not rethrown, Spring's transactional proxy never sees it — so a transaction that logically should roll back due to a failure may instead **commit partially-completed, inconsistent data**, since Spring only triggers rollback in response to an exception propagating out of the `@Transactional` method (by default, unchecked exceptions).
