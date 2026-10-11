# Spring Boot Interview Questions & Answers — Complete Guide

A comprehensive, Spring-Boot-focused reference covering auto-configuration internals, starters, configuration/properties, embedded servers, Actuator, testing, packaging/deployment, Spring Boot 3/native images, and production best practices.

---

## Table of Contents
1. [Spring Boot Fundamentals](#1-spring-boot-fundamentals)
2. [Auto-Configuration Deep Dive](#2-auto-configuration-deep-dive)
3. [Starters & Dependency Management](#3-starters--dependency-management)
4. [Project Structure & Bootstrapping](#4-project-structure--bootstrapping)
5. [Externalized Configuration & Properties](#5-externalized-configuration--properties)
6. [Profiles & Environments](#6-profiles--environments)
7. [Embedded Servers](#7-embedded-servers)
8. [Spring Boot DevTools](#8-spring-boot-devtools)
9. [Building REST APIs with Spring Boot](#9-building-rest-apis-with-spring-boot)
10. [Validation](#10-validation)
11. [Spring Boot & Data Access](#11-spring-boot--data-access)
12. [Spring Boot Security](#12-spring-boot-security)
13. [Actuator, Metrics & Observability](#13-actuator-metrics--observability)
14. [Logging](#14-logging)
15. [Testing in Spring Boot](#15-testing-in-spring-boot)
16. [Scheduling, Async & Task Execution](#16-scheduling-async--task-execution)
17. [Packaging & Deployment](#17-packaging--deployment)
18. [Spring Boot 3 & GraalVM Native Images](#18-spring-boot-3--graalvm-native-images)
19. [Messaging, Batch & Integration](#19-messaging-batch--integration)
20. [Performance, Troubleshooting & Best Practices](#20-performance-troubleshooting--best-practices)

---

## 1. Spring Boot Fundamentals

**Q1. What is Spring Boot, and what core problems does it solve?**
Spring Boot is an opinionated framework built on top of the Spring Framework that eliminates most of the manual configuration traditionally required to build Spring applications. It solves: (1) **configuration overhead** — via auto-configuration, sensible defaults are applied automatically based on the classpath; (2) **dependency management complexity** — via curated "starter" POMs with compatible versions; (3) **deployment friction** — via embedded servers producing a single executable JAR instead of requiring an external application server; (4) **lack of production readiness** — via Actuator, providing health checks/metrics out of the box.

**Q2. What are the four key pillars/features of Spring Boot?**
1. **Auto-Configuration** — automatically configures beans based on classpath contents and existing configuration.
2. **Starter Dependencies** — curated, version-compatible dependency bundles.
3. **Embedded Servers** — Tomcat/Jetty/Undertow bundled directly into the application, no external deployment needed.
4. **Production-Ready Features (Actuator)** — health checks, metrics, and monitoring endpoints built in.

**Q3. What is the difference between Spring and Spring Boot?**
Spring Framework provides the core IoC container, DI, AOP, and abstractions (MVC, data access) but requires substantial manual configuration (XML or Java config) to wire everything together and typically requires deployment to an external servlet container. Spring Boot is built **on top of** Spring, adding auto-configuration, starters, and embedded servers to drastically reduce that setup burden — Spring Boot doesn't replace Spring; it's a convention-over-configuration layer around it.

**Q4. What is `@SpringBootApplication`, and what does it do under the hood?**
A convenience meta-annotation combining three annotations:
- `@SpringBootConfiguration` (a specialized `@Configuration`, marking the class as a bean definition source).
- `@EnableAutoConfiguration` (triggers the auto-configuration mechanism based on classpath contents).
- `@ComponentScan` (scans for `@Component`-annotated classes starting from the annotated class's package).
This is why the main application class conventionally sits at the root of the project's package structure — so component scanning covers all sub-packages.

**Q5. What happens when you call `SpringApplication.run()`?**
It bootstraps the application: creates and configures the appropriate `ApplicationContext` type (e.g., `AnnotationConfigServletWebServerApplicationContext` for a web app), applies initializers and listeners, triggers the auto-configuration process, starts the embedded web server (if applicable), runs any `CommandLineRunner`/`ApplicationRunner` beans, and returns the fully-refreshed `ApplicationContext`.

**Q6. What is the Spring Boot Maven/Gradle plugin used for?**
It repackages the compiled application into an executable "fat/uber JAR" (bundling all dependencies plus an embedded server in a nested-JAR layout), provides a `bootRun`/`spring-boot:run` goal for running the app directly during development, and supports building OCI/Docker images (`bootBuildImage`) without a separate Dockerfile.

---

## 2. Auto-Configuration Deep Dive

**Q7. How does Spring Boot's auto-configuration mechanism actually work?**
Auto-configuration classes are conditionally applied `@Configuration` classes, registered via a file Spring Boot reads at startup: `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` (Spring Boot 2.7+; earlier versions used `META-INF/spring.factories`). Each auto-configuration class is annotated with conditional annotations that determine whether it should actually apply, based on the classpath, existing beans, and properties.

**Q8. What are the key `@Conditional` annotations used in auto-configuration, and what does each check?**
- `@ConditionalOnClass`/`@ConditionalOnMissingClass`: whether a specified class is (or isn't) present on the classpath.
- `@ConditionalOnBean`/`@ConditionalOnMissingBean`: whether a bean of a given type is (or isn't) already registered — this is what allows user-defined beans to "override" auto-configured defaults.
- `@ConditionalOnProperty`: whether a specific property is set (and optionally matches a specific value).
- `@ConditionalOnWebApplication`/`@ConditionalOnNotWebApplication`: whether the application context is a web application.
- `@ConditionalOnResource`: whether a specific resource (e.g., a file) exists on the classpath.
- `@ConditionalOnExpression`: evaluates a SpEL expression.

**Q9. Why does `@ConditionalOnMissingBean` matter so much for extensibility?**
It's the mechanism that lets your own bean definitions **take precedence** over Spring Boot's defaults — if you define your own `DataSource` bean, Spring Boot's auto-configured `DataSource` backs off automatically because the condition (`@ConditionalOnMissingBean(DataSource.class)`) is no longer satisfied. This is what makes auto-configuration "opinionated defaults you can override," not "configuration you're locked into."

**Q10. How do you view which auto-configurations were applied (or not) at startup?**
Run the application with `--debug` (or set `debug=true` in properties) — Spring Boot prints an **auto-configuration report** at startup, listing "Positive matches" (applied), "Negative matches" (skipped, with the reason), and "Exclusions." Alternatively, hit the Actuator `/actuator/conditions` endpoint for the same information at runtime.

**Q11. How do you exclude a specific auto-configuration class?**
```java
@SpringBootApplication(exclude = { DataSourceAutoConfiguration.class })
```
Or via properties: `spring.autoconfigure.exclude=org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration`. Common use case: excluding `DataSourceAutoConfiguration` when a `DataSource` isn't needed/available yet, to prevent startup failure.

**Q12. What is the difference between `@Configuration` auto-configuration ordering — how does Spring Boot control which auto-configurations apply before/after others?**
Via `@AutoConfigureBefore`, `@AutoConfigureAfter`, and `@AutoConfigureOrder` — since some auto-configurations depend on beans created by others (e.g., a JPA auto-configuration needs a `DataSource` to already exist), explicit ordering ensures dependencies are configured in the correct sequence.

**Q13. Can you write your own custom auto-configuration (e.g., for an internal shared library)? What's involved?**
Yes. Create a `@AutoConfiguration` (or `@Configuration(proxyBeanMethods = false)`) class with appropriate `@Conditional*` annotations defining your beans, then register it by listing its fully qualified class name in `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` within your library's JAR. Any application that includes the library as a dependency will then automatically pick up the auto-configuration — this is exactly how third-party Spring Boot "starters" work.

**Q14. What is `@EnableAutoConfiguration`, and how does it differ from `@ComponentScan`?**
`@EnableAutoConfiguration` triggers Spring Boot to look for and conditionally apply auto-configuration classes from the classpath (including third-party libraries) — it's about **framework/library-level default beans**. `@ComponentScan` discovers and registers **your own application's** `@Component`-annotated classes within specified packages — a completely separate mechanism for a different purpose, though both contribute beans to the same `ApplicationContext`.

---

## 3. Starters & Dependency Management

**Q15. What is a Spring Boot starter?**
A special dependency descriptor (a POM with no code of its own, just transitive dependencies) that bundles a set of commonly-used-together, version-compatible libraries under one artifact — e.g., `spring-boot-starter-web` pulls in Spring MVC, Jackson, Bean Validation, and an embedded Tomcat in one declaration, instead of manually specifying and version-matching each library.

**Q16. What are some of the most commonly used Spring Boot starters?**
- `spring-boot-starter-web`: building web apps/REST APIs with Spring MVC (embedded Tomcat by default).
- `spring-boot-starter-webflux`: reactive web applications.
- `spring-boot-starter-data-jpa`: JPA/Hibernate-based data access.
- `spring-boot-starter-security`: Spring Security integration.
- `spring-boot-starter-test`: testing libraries (JUnit 5, Mockito, AssertJ, Spring Test).
- `spring-boot-starter-validation`: Bean Validation (Jakarta Validation/Hibernate Validator).
- `spring-boot-starter-actuator`: production-ready monitoring endpoints.
- `spring-boot-starter-thymeleaf`: server-side template rendering.

**Q17. What is the Spring Boot "Bill of Materials" (BOM), and why does it matter?**
`spring-boot-dependencies` (imported via `<parent>` inheritance or `<dependencyManagement>` import scope) centrally defines **tested, compatible version numbers** for a huge range of common third-party libraries used across the Spring ecosystem — so when you declare a dependency without a version, Maven/Gradle picks up the version Spring Boot has verified works correctly with that Boot release, avoiding manual version conflict resolution ("dependency hell").

**Q18. How do you override a dependency version managed by the Spring Boot BOM?**
Explicitly specify a `<version>` for that dependency in your own POM (Maven) — this overrides the BOM-managed version — though doing so risks compatibility issues, since the BOM's versions were specifically tested together; override cautiously and test thoroughly.

**Q19. What is the difference between inheriting from `spring-boot-starter-parent` vs. importing `spring-boot-dependencies` as a BOM?**
`spring-boot-starter-parent` is a full parent POM — provides the BOM's dependency management, **plus** default plugin configuration, resource filtering, and a default Java version — but requires your project to use it as its single parent, which can conflict if your organization already has its own corporate parent POM. Importing `spring-boot-dependencies` via `<dependencyManagement><scope>import</scope>` gives you just the version management benefits without requiring it to be your project's parent — used when you need a different/existing parent POM.

---

## 4. Project Structure & Bootstrapping

**Q20. What is the conventional Maven/Gradle project structure for a Spring Boot application?**
```
src/main/java/com/example/app/    → application code, main class at the root package
src/main/resources/
    application.yml               → configuration
    static/                       → static web assets (served directly)
    templates/                    → server-side view templates (Thymeleaf, etc.)
src/test/java/...                 → test code
```

**Q21. Why does the main `@SpringBootApplication` class's package location matter?**
Because `@ComponentScan` (bundled within `@SpringBootApplication`) scans the package of the annotated class **and all its sub-packages** by default — placing the main class in a non-root package means sibling/parent packages won't be scanned, silently causing beans in those packages to never be registered. Placing it at the top-level package avoids this entirely.

**Q22. What is `CommandLineRunner` vs. `ApplicationRunner`, and how are multiple runners ordered?**
Both are functional interfaces for running code right after the application context has fully started. `CommandLineRunner.run(String... args)` receives raw command-line arguments; `ApplicationRunner.run(ApplicationArguments args)` receives a parsed representation (distinguishing option args like `--foo=bar` from non-option args). If multiple runner beans exist, execution order can be controlled via `@Order` or by implementing `Ordered`.

**Q23. What is the Spring Boot banner, and can it be customized?**
The ASCII-art "Spring" logo printed to the console at startup. It can be customized by placing a `banner.txt` file in `src/main/resources` (supporting placeholders like `${spring-boot.version}`), replaced with an image (`banner.gif`/`.jpg`/`.png`), or disabled entirely via `spring.main.banner-mode=off`.

**Q24. What are `ApplicationContextInitializer` and `SpringApplicationRunListener`, and when would you use them?**
Both are extension points that hook into the application startup lifecycle **earlier** than `CommandLineRunner` (which runs only after the context is fully refreshed). `ApplicationContextInitializer` lets you programmatically customize the `ApplicationContext` before beans are loaded (e.g., adding property sources). `SpringApplicationRunListener` provides callbacks at very fine-grained startup phases (starting, environment prepared, context prepared, started, failed) — used mostly by frameworks/infrastructure code rather than typical application logic.

---

## 5. Externalized Configuration & Properties

**Q25. What is externalized configuration in Spring Boot, and why is it important?**
The practice of keeping configuration values (database URLs, credentials, feature flags) outside the compiled application code, so the **same build artifact** can be deployed across different environments (dev/test/prod) with different configuration, without recompiling — a core tenet of the "build once, deploy anywhere"/twelve-factor app methodology.

**Q26. What is the property source precedence order in Spring Boot (highest to lowest priority)?**
Roughly (Spring Boot resolves higher-priority sources first, and they override lower ones):
1. Command-line arguments (`--server.port=8081`).
2. `SPRING_APPLICATION_JSON` (embedded JSON in an env variable/system property).
3. `ServletConfig`/`ServletContext` init parameters.
4. JNDI attributes.
5. Java System properties (`-Dkey=value`).
6. OS environment variables.
7. Profile-specific properties outside the packaged jar (`application-{profile}.yml`).
8. Profile-specific properties packaged inside the jar.
9. Application properties outside the packaged jar (`application.yml`).
10. Application properties packaged inside the jar.
11. `@PropertySource` annotations.
12. Default properties (`SpringApplication.setDefaultProperties`).

**Q27. What is the difference between `application.properties` and `application.yml`, and can you use both?**
Functionally equivalent formats for the same configuration — `.properties` uses flat dot-notation key-value pairs; `.yml` uses YAML's indentation-based hierarchy (more concise for deeply nested config, native list support). You technically *can* have both, but it's discouraged since it becomes unclear which takes precedence for overlapping keys (properties files generally take precedence over YAML if both exist at the same classpath location) — pick one format per project for consistency.

**Q28. What is `@ConfigurationProperties`, and what are its advantages over `@Value`?**
Binds a whole block of related, hierarchical properties onto a strongly-typed POJO in one step:
```java
@ConfigurationProperties(prefix = "app.mail")
public class MailProperties {
    private String host;
    private int port;
    // getters/setters
}
```
Advantages over scattering individual `@Value("${app.mail.host}")` injections: type-safety, support for nested objects/lists/maps, relaxed binding (kebab-case, camelCase, UPPER_CASE env vars all map to the same property), and built-in validation support (`@Validated` + Bean Validation annotations on the properties class).

**Q29. What is "relaxed binding" in Spring Boot configuration?**
Spring Boot's flexible property-name matching that allows the same logical property to be specified in different naming conventions depending on the source — e.g., `app.my-service.api-key` (kebab-case in YAML), `app.myService.apiKey` (camelCase), and `APP_MYSERVICE_APIKEY` (environment variable, since env vars can't contain dots/dashes) all bind to the same `@ConfigurationProperties` field, making it seamless to configure the same application via files, env vars, or system properties.

**Q30. How do you enable and validate `@ConfigurationProperties`-bound configuration?**
Register the properties class (via `@EnableConfigurationProperties(MailProperties.class)` on a config class, or by annotating the properties class itself with `@Component`/`@ConfigurationProperties` combined with component scanning). Add `@Validated` on the properties class plus standard Bean Validation annotations (`@NotNull`, `@Min`) on its fields — Spring Boot will fail fast at startup with a clear error if the bound configuration is invalid.

**Q31. What is `spring.config.import`, and what's it used for?**
A property (Spring Boot 2.4+) that lets you explicitly import additional configuration sources beyond the default `application.yml`, such as an external config server, another properties file, or (with the right starter) a config server backend: `spring.config.import=optional:configserver:http://localhost:8888` — replacing the older `bootstrap.yml`-based mechanism used with Spring Cloud Config.

**Q32. How do you securely manage secrets (passwords, API keys) in Spring Boot configuration?**
Avoid committing secrets to source control. Options: environment variables (injected at deploy time, e.g., via Kubernetes Secrets or a CI/CD pipeline), a dedicated secrets manager (HashiCorp Vault, AWS Secrets Manager, Azure Key Vault) integrated via Spring Cloud Vault or a custom `PropertySource`, or encrypted properties (e.g., Jasypt) decrypted at runtime — never hardcoding secrets directly in `application.yml` committed to a repository.

---

## 6. Profiles & Environments

**Q33. What are Spring Profiles, and how do you activate one?**
A way to segregate configuration/beans by environment (`dev`, `test`, `prod`, etc.). Activate via the `spring.profiles.active` property, settable through any configuration source: `--spring.profiles.active=prod` (command line), `SPRING_PROFILES_ACTIVE=prod` (environment variable), or within `application.yml`.

**Q34. How do you define profile-specific beans and configuration?**
- Beans: annotate with `@Profile("dev")` on a `@Component`/`@Bean` definition — the bean is only registered when that profile is active.
- Properties: create `application-{profile}.yml` (e.g., `application-prod.yml`) — its properties override/supplement the base `application.yml` when that profile is active. In a single YAML file, you can also use `---` document separators with `spring.config.activate.on-profile: prod`.

**Q35. What is the difference between `spring.profiles.active` and `spring.profiles.default`?**
`spring.profiles.active` explicitly specifies which profile(s) to activate. `spring.profiles.default` specifies a fallback profile to use **only if** no profile is explicitly activated via `spring.profiles.active` through any source — useful for defining a sensible default (e.g., `dev`) when nothing else is specified.

**Q36. Can multiple profiles be active simultaneously?**
Yes — `spring.profiles.active=prod,cloud,monitoring` activates all three simultaneously; properties from each are merged (with later ones in the list taking precedence on conflicts), and beans conditioned on any of those active profiles are included.

**Q37. What are Spring profile groups (`spring.profiles.group`), introduced in Spring Boot 2.4?**
Allow bundling several individual profiles under a single umbrella profile name, so activating one "group" profile automatically activates all the profiles within it — e.g., `spring.profiles.group.production=proddb,prodcache,prodsecurity` lets you activate all three just by specifying `--spring.profiles.active=production`.

---

## 7. Embedded Servers

**Q38. What embedded servers does Spring Boot support out of the box, and what is the default for Spring MVC-based apps?**
**Tomcat** (default, via `spring-boot-starter-web`), **Jetty**, and **Undertow** — all bundled directly within the application JAR, eliminating the need for a separately-installed application server. For reactive (WebFlux) apps, **Netty** is the default.

**Q39. How do you switch the embedded server from Tomcat to Jetty (or Undertow)?**
Exclude the default Tomcat starter dependency from `spring-boot-starter-web`, then add the desired server's starter:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```

**Q40. How do you configure the embedded server's port and other properties?**
Via properties: `server.port=8081`, `server.servlet.context-path=/api`, `server.tomcat.max-threads=200` (Tomcat-specific tuning), `server.compression.enabled=true`. Setting `server.port=0` binds to a random available port — commonly used in integration tests.

**Q41. How do you enable HTTPS/SSL for the embedded server?**
Configure a keystore and enable SSL via properties:
```yaml
server:
  port: 8443
  ssl:
    key-store: classpath:keystore.p12
    key-store-password: changeit
    key-store-type: PKCS12
```
In production, TLS termination is often handled by a load balancer/reverse proxy instead, with the Spring Boot app itself running plain HTTP internally.

**Q42. What is graceful shutdown, and how do you enable it in Spring Boot?**
Ensures in-flight requests are allowed to complete (up to a configured timeout) before the application shuts down, rather than abruptly terminating active connections — important in orchestrated environments (Kubernetes) during rolling deployments/scale-downs. Enabled via `server.shutdown=graceful` combined with `spring.lifecycle.timeout-per-shutdown-phase=30s`.

**Q43. Can a Spring Boot application still be deployed as a traditional WAR to an external servlet container?**
Yes — by extending `SpringBootServletInitializer` and overriding `configure()`, and changing the packaging to `war` with the embedded container dependency marked `provided` scope. This is less common now (most deployments use the embedded-server executable JAR approach, especially with containers/Kubernetes), but remains supported for organizations with existing app-server infrastructure requirements.

---

## 8. Spring Boot DevTools

**Q44. What is Spring Boot DevTools, and what features does it provide?**
A development-time-only dependency (`spring-boot-devtools`) that improves the development feedback loop: **automatic restart** (monitors classpath changes and restarts the app quickly by using two classloaders — a fast-reloading one for your code, a stable one for dependencies), **LiveReload** (auto-refreshes the browser on changes), and sensible **development-time property defaults** (e.g., disabling template caching).

**Q45. How does DevTools' automatic restart achieve faster restarts than a full JVM restart?**
It uses two separate classloaders: a "base" classloader for unchanging third-party library classes, and a "restart" classloader for your application's own classes. When a change is detected, only the restart classloader is discarded and recreated (reloading just your application code), which is significantly faster than restarting the entire JVM and reloading every dependency from scratch.

**Q46. Why is it important to ensure DevTools doesn't end up in a production build?**
DevTools is intended purely for local development. Its dependency is marked `optional`/`developmentOnly` in Spring Boot's starter configuration precisely so it's automatically excluded from production artifacts by default when using the standard Boot Maven/Gradle plugin — including it in production could expose unwanted behaviors (e.g., property defaults meant for dev, or the remote debug/restart endpoint if misconfigured).

---

## 9. Building REST APIs with Spring Boot

**Q47. How does Spring Boot auto-configure Jackson for JSON serialization?**
When `spring-boot-starter-web` (or `-json`) is on the classpath, Spring Boot auto-configures an `ObjectMapper` bean with sensible defaults and registers it as the `HttpMessageConverter` used for converting Java objects to/from JSON automatically for `@RestController` methods — customizable via `spring.jackson.*` properties (e.g., `spring.jackson.default-property-inclusion=non_null`) or by defining your own `Jackson2ObjectMapperBuilderCustomizer` bean.

**Q48. How do you customize JSON serialization for a specific field (e.g., excluding it, renaming it, or formatting a date)?**
Using Jackson annotations directly on the DTO/entity: `@JsonIgnore` (exclude a field), `@JsonProperty("customName")` (rename), `@JsonFormat(pattern = "yyyy-MM-dd")` (format dates), `@JsonInclude(Include.NON_NULL)` (omit null fields from output).

**Q49. How do you globally handle exceptions and return consistent error responses across a Spring Boot REST API?**
Using `@RestControllerAdvice` combined with `@ExceptionHandler` methods, centralizing exception-to-HTTP-response translation logic. Spring Boot 3+ also natively supports the **RFC 7807 "Problem Details"** standard for structured error responses via `ProblemDetail`, which can be returned directly from exception handlers for a standardized error format.

**Q50. How does Spring Boot handle unhandled exceptions by default if you haven't defined a custom handler?**
It falls back to the built-in `BasicErrorController` (or `DefaultErrorAttributes`), which produces a generic JSON error response (containing `timestamp`, `status`, `error`, `path`, and optionally `message`/`trace` depending on configuration) mapped to `/error` — customizable via `server.error.*` properties or by providing your own `ErrorController` implementation.

**Q51. What is `spring-boot-starter-web` vs `spring-boot-starter-webflux`, and can they coexist?**
`-web` provides the traditional, blocking Spring MVC stack (Tomcat by default). `-webflux` provides the reactive, non-blocking stack (Netty by default). While technically possible to include both on the classpath, Spring Boot auto-configuration is designed to configure **one or the other** based on what's present (favoring Spring MVC if both are on the classpath, per Spring Boot's WebApplicationType detection) — mixing them intentionally in one application is unusual and not the standard use case.

---

## 10. Validation

**Q52. How do you enable and use Bean Validation in a Spring Boot REST API?**
Add `spring-boot-starter-validation` (bundles Hibernate Validator, the reference Jakarta Bean Validation implementation). Annotate DTO fields with constraints (`@NotNull`, `@NotBlank`, `@Size`, `@Email`, `@Min`/`@Max`, `@Pattern`), then annotate the controller parameter with `@Valid`:
```java
@PostMapping("/users")
public ResponseEntity<User> create(@Valid @RequestBody UserDto dto) { ... }
```
Failed validation automatically throws `MethodArgumentNotValidException`, resulting in a `400 Bad Request` by default (customizable via a `@RestControllerAdvice` handler for structured error output).

**Q53. What is the difference between `@Valid` and `@Validated`?**
`@Valid` (standard Jakarta Bean Validation annotation) triggers validation on method parameters/return values and supports nested object validation (cascading). `@Validated` (Spring's own annotation) additionally supports **validation groups** (applying different constraint subsets in different contexts, e.g., "create" vs. "update" validation rules) and is required (at the class level) to enable method-level validation on plain service-layer methods (not just controller parameters).

**Q54. How do you write a custom validation constraint?**
Define a custom annotation plus a `ConstraintValidator` implementation:
```java
@Constraint(validatedBy = PhoneNumberValidator.class)
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface ValidPhoneNumber {
    String message() default "Invalid phone number";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}
```

---

## 11. Spring Boot & Data Access

**Q55. How does Spring Boot auto-configure a `DataSource`?**
If a JDBC driver and `spring-boot-starter-data-jpa` (or `-jdbc`) are on the classpath, and connection properties (`spring.datasource.url/username/password`) are provided (or an embedded database like H2 is detected), Spring Boot auto-configures a `DataSource` bean automatically (using HikariCP as the default connection pool since Spring Boot 2), along with a `JdbcTemplate`, `EntityManagerFactory`, and `PlatformTransactionManager` as applicable — all overridable by defining your own beans.

**Q56. What connection pool does Spring Boot use by default, and can it be changed?**
**HikariCP** is the default connection pool (since Spring Boot 2.0), chosen for its performance and low overhead. It can be swapped for another pool (e.g., Apache DBCP2, Tomcat pool) by excluding HikariCP and including the alternative on the classpath along with `spring.datasource.type` set accordingly.

**Q57. How does `spring.jpa.hibernate.ddl-auto` work, and what are the risks of each setting?**
Controls automatic schema generation/validation by Hibernate at startup: `none` (no action), `validate` (checks schema matches entities, doesn't modify), `update` (attempts to alter the schema to match entities — **risky in production**, can cause unexpected/destructive changes), `create` (drops and recreates the schema on every startup — data loss), `create-drop` (like `create`, plus drops schema on shutdown — typically for tests). **Production best practice:** use `validate` or `none`, managing schema changes explicitly via a migration tool (Flyway/Liquibase).

**Q58. What are Flyway and Liquibase, and how does Spring Boot integrate with them?**
Both are database schema migration tools that apply versioned SQL/changeset scripts incrementally and track which migrations have already been applied — providing reliable, repeatable, auditable schema evolution across environments (far safer than `ddl-auto=update`). Spring Boot auto-configures either one automatically if the corresponding starter (`spring-boot-starter-flyway` dependency or `liquibase-core`) is on the classpath, running pending migrations automatically at application startup.

**Q59. How does Spring Boot auto-configure an embedded database like H2 for testing?**
If an embedded database driver (H2, HSQLDB, Derby) is on the classpath and no explicit `spring.datasource.url` is configured, Spring Boot automatically creates and configures an in-memory instance — commonly used for `@DataJpaTest` slice tests to avoid needing a real external database for fast, isolated tests (though Testcontainers is increasingly preferred for higher-fidelity integration tests against the real production database engine).

**Q60. What is `spring.jpa.open-in-view`, and why does Spring Boot log a warning about it by default?**
The **Open Session in View (OSIV)** pattern keeps the Hibernate session/persistence context open through the entire web request (including view rendering), allowing lazy-loaded associations to be accessed even outside the original transactional service method — convenient, but it can mask N+1 query problems, holds a database connection for the request's full duration (harming scalability under load), and blurs transactional boundaries. Spring Boot enables it by default for convenience but logs a warning recommending it be explicitly set (`spring.jpa.open-in-view=false` for stricter, more scalable architecture, handling lazy loading explicitly within the service layer instead).

---

## 12. Spring Boot Security

**Q61. What happens automatically when you add `spring-boot-starter-security` with no further configuration?**
Spring Boot auto-configures a default security setup: **every** endpoint requires authentication, a default in-memory user (username `user`) is generated with a randomly-generated password (printed to the console at startup), HTTP Basic and form-login authentication are both enabled by default, and CSRF protection is enabled for browser-based (session) authentication.

**Q62. How do you define custom security rules in a Spring Boot application (Spring Boot 3+/Spring Security 6+)?**
Define a `SecurityFilterChain` bean (the `WebSecurityConfigurerAdapter` class-extension approach is removed as of Spring Security 6):
```java
@Bean
SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    http.authorizeHttpRequests(auth -> auth
            .requestMatchers("/api/public/**").permitAll()
            .anyRequest().authenticated())
        .httpBasic(Customizer.withDefaults())
        .csrf(csrf -> csrf.disable()); // typically disabled for stateless REST APIs
    return http.build();
}
```

**Q63. How do you disable Spring Security's auto-configured default security entirely (e.g., temporarily during development)?**
Either remove the `spring-boot-starter-security` dependency, or exclude the relevant auto-configuration classes (`SecurityAutoConfiguration`, `UserDetailsServiceAutoConfiguration`) — though it's generally better practice to configure appropriate (even permissive, for local dev) security rules explicitly rather than disabling security outright.

**Q64. How does Spring Boot auto-configure JWT-based resource server security?**
With `spring-boot-starter-oauth2-resource-server` on the classpath and `spring.security.oauth2.resourceserver.jwt.issuer-uri` (or `jwk-set-uri`) configured, Spring Boot auto-configures a `JwtDecoder` that validates incoming bearer tokens' signatures and claims automatically against the specified authorization server — enabling `.oauth2ResourceServer(oauth2 -> oauth2.jwt(Customizer.withDefaults()))` in the security filter chain configuration.

---

## 13. Actuator, Metrics & Observability

**Q65. What is Spring Boot Actuator, and how do you add it to a project?**
A sub-project providing built-in production-monitoring endpoints. Added via the `spring-boot-starter-actuator` dependency — once present, it auto-configures a set of endpoints (most exposed only over JMX by default; HTTP exposure must be explicitly enabled for most beyond `health`).

**Q66. Which Actuator endpoints are exposed over HTTP by default, and how do you expose more?**
By default, only `/actuator/health` is exposed over HTTP (for security reasons). To expose additional endpoints:
```yaml
management:
  endpoints:
    web:
      exposure:
        include: health, info, metrics, loggers
        # or: include: "*" (exposes everything — use cautiously)
```

**Q67. What does the `/actuator/health` endpoint show, and how do you add a custom health check?**
Reports overall application health, aggregating multiple `HealthIndicator` beans (auto-configured ones check things like database connectivity, disk space, message broker connectivity). Custom health checks are added by implementing the `HealthIndicator` interface (or extending `AbstractHealthIndicator`) as a bean:
```java
@Component
public class CustomHealthIndicator implements HealthIndicator {
    public Health health() {
        return isDependencyHealthy() ? Health.up().build() : Health.down().withDetail("reason", "...").build();
    }
}
```

**Q68. What is `management.endpoint.health.show-details`, and why does it matter for security?**
Controls how much detail the health endpoint reveals (`never`, `when-authorized`, `always`). Exposing full details (e.g., internal component statuses, error messages) to unauthenticated/public clients can leak sensitive infrastructure information — production systems typically restrict this to `when-authorized` (requiring an authenticated, authorized caller) or secure the endpoint entirely behind authentication.

**Q69. What is Micrometer, and how does it relate to Actuator's `/actuator/metrics` endpoint?**
Micrometer is a vendor-neutral instrumentation facade (analogous to SLF4J for logging) that Spring Boot uses internally to collect application metrics (JVM memory/GC stats, HTTP request timings/counts, custom business metrics) and expose them through a consistent API regardless of the monitoring backend. Actuator's `/actuator/metrics` endpoint surfaces these metrics directly; `/actuator/prometheus` (with the `micrometer-registry-prometheus` dependency) exposes them in Prometheus's scrape format for external monitoring systems.

**Q70. How do you create a custom application metric using Micrometer?**
Inject a `MeterRegistry` and register a counter, timer, or gauge:
```java
@Autowired MeterRegistry registry;

Counter ordersCounter = registry.counter("orders.processed");
ordersCounter.increment();
```

**Q71. What is distributed tracing in Spring Boot 3, and what replaced Spring Cloud Sleuth?**
**Micrometer Tracing** (bundled starting with Spring Boot 3) replaced the now-retired Spring Cloud Sleuth, providing the same core capability — automatically instrumenting requests with trace/span IDs and propagating them across service boundaries — with pluggable bridges to tracing backends like Zipkin or OpenTelemetry (`micrometer-tracing-bridge-otel` / `-brave`).

**Q72. How do you secure Actuator endpoints in production?**
Combine: exposing only the endpoints actually needed (`management.endpoints.web.exposure.include=health,info,metrics`), securing them behind Spring Security authorization rules (e.g., requiring an `ADMIN` role for `/actuator/**` beyond `/actuator/health`), and/or moving Actuator to a **separate management port** (`management.server.port=9001`) not exposed to the public internet, only accessible internally/to monitoring infrastructure.

---

## 14. Logging

**Q73. What logging framework does Spring Boot use by default?**
**Logback**, accessed through the **SLF4J** facade — Spring Boot auto-configures sensible console/file logging defaults out of the box, requiring no additional setup for basic use.

**Q74. How do you configure logging levels in Spring Boot?**
Via properties: `logging.level.root=WARN`, `logging.level.com.example.myapp=DEBUG` (package/class-specific level overrides) — or dynamically at runtime through the Actuator `/actuator/loggers` endpoint (POST to change a logger's level without restarting the application).

**Q75. How do you switch from Logback to Log4j2 in Spring Boot?**
Exclude the default `spring-boot-starter-logging` dependency and include `spring-boot-starter-log4j2` instead — Spring Boot's auto-configuration adapts accordingly since it detects the logging implementation present on the classpath.

**Q76. How do you configure structured (JSON) logging output in Spring Boot?**
Spring Boot 3.4+ provides built-in structured logging support (`logging.structured.format.console=ecs` or `logstash`/`gelf`), or historically via a custom Logback/Log4j2 configuration with a JSON encoder (e.g., `logstash-logback-encoder`) — important for log aggregation systems (ELK, Splunk) that parse structured fields rather than free-form text.

**Q77. What is MDC (Mapped Diagnostic Context), and how is it used in Spring Boot logging?**
A mechanism (from SLF4J/Logback) for attaching contextual key-value data (e.g., a request ID, user ID, trace ID) to the current thread, which then automatically appears in every log statement made on that thread — extremely useful for correlating all log lines belonging to a single request/transaction, especially combined with a logging pattern that includes MDC values.

---

## 15. Testing in Spring Boot

**Q78. What does `spring-boot-starter-test` include by default?**
JUnit 5 (Jupiter), Mockito, AssertJ (fluent assertions), Hamcrest, JSONassert, and Spring Test/Spring Boot Test utilities — a comprehensive default testing toolkit requiring no additional individual test-library dependencies for most needs.

**Q79. What is the difference between `@SpringBootTest` and the various "slice" test annotations?**
`@SpringBootTest` loads the **entire** application context, suitable for full integration tests but slower due to initializing every bean. **Slice annotations** load only the beans relevant to a specific layer, for faster, focused tests:
- `@WebMvcTest(MyController.class)`: loads only MVC infrastructure + the specified controller; service-layer beans must be mocked (`@MockBean`).
- `@DataJpaTest`: loads only JPA/repository components, auto-configures an embedded/test database, and wraps each test in a rolled-back transaction.
- `@JsonTest`: loads only JSON serialization infrastructure (Jackson).
- `@RestClientTest`: for testing REST client code, auto-configuring `MockRestServiceServer`.

**Q80. What is `@MockBean`, and how does it differ from plain Mockito `@Mock`?**
`@MockBean` (Spring Boot Test) creates a Mockito mock **and registers/replaces it within the Spring `ApplicationContext`**, so any bean depending on that type gets the mock injected automatically by the container — used specifically in Spring context-based tests. Plain `@Mock` (pure Mockito) creates a standalone mock with no Spring context involvement, used in simple unit tests instantiating the class under test directly.

**Q81. How do you test a full HTTP request/response cycle in Spring Boot (as opposed to calling a controller method directly)?**
Use `@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)` combined with `TestRestTemplate` (blocking, MVC) or `WebTestClient` (reactive, or for MVC apps too) to make actual HTTP calls against a running embedded server instance during the test — verifying the full stack including serialization, routing, and (if not mocked) security filters.

**Q82. What is Testcontainers, and why has it become a standard tool in Spring Boot integration testing?**
A library that programmatically launches real, ephemeral Docker containers (an actual PostgreSQL, Kafka, Redis instance, etc.) for the duration of a test suite — giving much higher-fidelity integration tests than in-memory substitutes (e.g., catching PostgreSQL-specific SQL behavior an H2 in-memory database would miss entirely). Spring Boot 3.1+ added first-class support via `@ServiceConnection` and the **Testcontainers Spring Boot integration**, automatically wiring container connection details into the Spring context without manual property configuration.

**Q83. What is `@TestConfiguration`, and how does it differ from `@Configuration`?**
Marks a configuration class intended **specifically for use in tests**, which Spring Boot's component scanning automatically excludes from the main application context (preventing test-only beans from accidentally leaking into production configuration) — used to define mock/stub beans or test-specific overrides that should only apply within the test context that explicitly imports them.

**Q84. How do you test `@ConfigurationProperties`-bound classes in isolation?**
`@EnableConfigurationProperties` combined with `@ConfigurationPropertiesScan`, or more simply, using `ApplicationContextRunner` (a lightweight utility for testing auto-configuration/context-loading logic in isolation without a full `@SpringBootTest`) to load just the properties class and assert on its bound values given a set of test properties.

---

## 16. Scheduling, Async & Task Execution

**Q85. How do you enable and use scheduled tasks in Spring Boot?**
Add `@EnableScheduling` on a configuration class, then annotate methods with `@Scheduled`:
```java
@Scheduled(fixedRate = 5000)       // every 5 seconds, measured from start-to-start
@Scheduled(fixedDelay = 5000)      // 5 seconds after the previous execution completes
@Scheduled(cron = "0 0 * * * *")   // cron expression (top of every hour)
public void runTask() { ... }
```

**Q86. What is the difference between `fixedRate` and `fixedDelay` in `@Scheduled`?**
`fixedRate` triggers executions at a constant interval measured from the **start** of each execution (can result in overlapping executions if a task runs longer than the interval, unless configured otherwise). `fixedDelay` waits the specified interval **after the previous execution completes** before starting the next — guarantees no overlap.

**Q87. How do you run asynchronous (non-blocking, background) methods in Spring Boot?**
Add `@EnableAsync` on a configuration class, then annotate the method with `@Async` — Spring executes it on a separate thread (from a configurable `TaskExecutor`) rather than the calling thread:
```java
@Async
public CompletableFuture<Result> processAsync() { ... }
```
Same self-invocation/proxy limitations as `@Transactional`/`@Cacheable` apply (calling an `@Async` method from within the same class bypasses the proxy).

**Q88. How do you configure a custom thread pool for `@Async` or `@Scheduled` tasks, rather than using Spring's default single-threaded executor?**
Define a custom `TaskExecutor` bean (e.g., `ThreadPoolTaskExecutor`) and reference it by name in the annotation: `@Async("customExecutor")` — important because Spring's default `SimpleAsyncTaskExecutor` creates a new thread per task with no pooling/limits, which can exhaust system resources under load in production.

**Q89. What is the default scheduler behavior if you don't configure a `TaskScheduler` bean, and why might that be a problem?**
Without an explicit `TaskScheduler` bean, `@Scheduled` tasks run on a **single-threaded** default scheduler — meaning if one scheduled task takes a long time, it delays all other scheduled tasks from running on time. Defining a dedicated `ThreadPoolTaskScheduler` bean with an appropriate pool size addresses this for applications with multiple scheduled jobs.

---

## 17. Packaging & Deployment

**Q90. What is a Spring Boot "fat/executable JAR," and how does its internal structure differ from a normal JAR?**
A single JAR containing the compiled application classes **plus all dependency JARs nested inside it** (under `BOOT-INF/lib/`), along with a custom `Main-Class` (`JarLauncher`) that knows how to load classes from these nested JARs at runtime — a capability the standard JVM classloader doesn't natively support, which is why Spring Boot provides its own launcher/classloading mechanism, allowing `java -jar app.jar` to run the entire self-contained application.

**Q91. How do you build a Docker image for a Spring Boot application?**
Options: (1) a traditional multi-stage `Dockerfile` copying the built JAR into a base JRE image; (2) the Spring Boot Maven/Gradle plugin's built-in **Cloud Native Buildpacks** support (`mvn spring-boot:build-image` / `./gradlew bootBuildImage`), which produces an optimized, layered OCI image without writing a Dockerfile at all, automatically using Paketo buildpacks.

**Q92. What are Spring Boot's "layered JARs," and why do they matter for Docker image efficiency?**
Since Spring Boot 2.3, the executable JAR can be built with distinct **layers** (dependencies, Spring Boot loader, snapshot dependencies, application classes) instead of one monolithic blob. When building a Docker image using these layers (via `docker-compose` extraction or the buildpack integration), Docker can cache unchanged layers (like third-party dependencies, which rarely change) separately from frequently-changing application code — resulting in much faster incremental image rebuilds, since only the changed layer needs re-uploading.

**Q93. How do you pass environment-specific configuration to a containerized Spring Boot application?**
Typically via environment variables (mapping to Spring properties through relaxed binding, e.g., `SPRING_DATASOURCE_URL`), mounted config files/ConfigMaps (Kubernetes), or command-line arguments passed to the container's entrypoint — avoiding baking environment-specific values into the image itself, keeping the same image portable across environments.

**Q94. What is a Spring Boot "smoke test" / readiness vs. liveness probe, in the context of Kubernetes deployment?**
Kubernetes uses **liveness probes** (is the app still running/should it be restarted?) and **readiness probes** (is the app ready to receive traffic?) to manage container health. Spring Boot Actuator exposes dedicated `/actuator/health/liveness` and `/actuator/health/readiness` endpoints (enabled via `management.endpoint.health.probes.enabled=true`, automatically enabled when running in a detected Kubernetes environment) specifically designed to back these Kubernetes probes.

**Q95. What is application versioning best practice for Spring Boot microservices in CI/CD?**
Common approaches: tagging Docker images with immutable identifiers (git commit SHA, semantic version), using build metadata exposed via the Actuator `/actuator/info` endpoint (populated from `build-info.properties`, generated via the Spring Boot Maven/Gradle plugin's `build-info` goal) to trace exactly which build is running in a given environment.

---

## 18. Spring Boot 3 & GraalVM Native Images

**Q96. What are the headline changes in Spring Boot 3 compared to Spring Boot 2?**
Baseline **Java 17+** requirement, migration from `javax.*` to **`jakarta.*`** namespace (following Jakarta EE's own migration), first-class **GraalVM native image** support, **Micrometer Tracing** replacing Spring Cloud Sleuth, built-in **Problem Details (RFC 7807)** support for error responses, and a general modernization aligned with Spring Framework 6.

**Q97. What is a GraalVM native image, and what benefits does it provide for Spring Boot applications?**
An ahead-of-time (AOT) compiled, standalone executable produced from a Java application using the GraalVM native-image tool, rather than running on a traditional JVM with just-in-time compilation. Benefits: **near-instant startup time** (milliseconds vs. seconds) and **significantly lower memory footprint** — highly valuable for serverless functions and containerized microservices where fast cold-start and low resource usage matter greatly.

**Q98. What are the trade-offs/challenges of using GraalVM native images with Spring Boot?**
Longer build times (AOT compilation is much slower than a normal build), stricter constraints around reflection/dynamic class loading/proxies (common in Spring's runtime bean processing) requiring explicit "reachability metadata" hints — largely handled automatically now via Spring's **AOT processing engine** (Spring Framework 6+ generates this metadata automatically at build time), but third-party libraries not yet native-image-aware can still require manual configuration; some dynamic/reflective features may behave differently or be unsupported.

**Q99. How do you build a native image for a Spring Boot application?**
Using the Spring Boot Gradle/Maven plugin's native support combined with the GraalVM native-image compiler (often via Cloud Native Buildpacks for a Docker-free build, or the GraalVM `native-maven-plugin`/`org.graalvm.buildtools.native` Gradle plugin directly): `./gradlew nativeCompile` or `mvn -Pnative native:compile`.

**Q100. What is Spring's AOT (Ahead-of-Time) processing engine, and how does it support native images?**
Introduced in Spring Framework 6/Spring Boot 3, it analyzes the application at **build time** (rather than purely at runtime, as traditional Spring does) to pre-compute bean definitions, proxy creation, and reflection metadata into generated source code/configuration — reducing the amount of runtime reflection/dynamic behavior needed, which is essential since GraalVM native image compilation has limited support for unconstrained runtime reflection.

---

## 19. Messaging, Batch & Integration

**Q101. How does Spring Boot auto-configure Kafka support?**
With `spring-kafka` on the classpath and `spring.kafka.bootstrap-servers` configured, Spring Boot auto-configures a `KafkaTemplate` (for producing messages) and the infrastructure needed for `@KafkaListener`-annotated consumer methods, along with sensible default serializer/deserializer and consumer group configuration overridable via `spring.kafka.*` properties.

**Q102. How does Spring Boot auto-configure RabbitMQ support?**
With `spring-boot-starter-amqp` on the classpath and `spring.rabbitmq.*` connection properties configured, Spring Boot auto-configures a `ConnectionFactory`, `RabbitTemplate` (for sending), and the listener container infrastructure for `@RabbitListener`-annotated methods.

**Q103. What is Spring Batch, and when would you reach for it in a Spring Boot application?**
A framework for building robust, reusable **batch processing** jobs (reading, processing, and writing large volumes of data), providing built-in support for chunk-based processing, transaction management per chunk, job restart/retry after failure, and step-by-step job execution tracking — appropriate for use cases like nightly ETL jobs, large file imports, or scheduled bulk data transformations, rather than typical request/response application logic.

**Q104. How does Spring Boot integrate with Spring Batch's job execution tracking?**
Spring Boot auto-configures a `JobRepository` (backed by a relational database, tracking job/step execution metadata for restartability and auditing) when `spring-boot-starter-batch` is present, and by default (as of Spring Boot 2.5+) does **not** automatically run all discovered `Job` beans on startup unless explicitly configured (`spring.batch.job.enabled=true` plus specifying which job to run), avoiding accidental batch job execution in non-batch contexts.

---

## 20. Performance, Troubleshooting & Best Practices

**Q105. What are common causes of slow Spring Boot application startup, and how do you diagnose/address them?**
Causes: excessive classpath scanning scope, too many eagerly-initialized singleton beans, heavyweight auto-configuration evaluation (many unused starters pulled in), and slow bean initialization logic (e.g., blocking calls in `@PostConstruct`). Diagnosis: use `--debug` for the auto-configuration report, or enable `spring.main.log-startup-info` and the startup **`ApplicationStartup`**/`BufferingApplicationStartup` tracking (Spring Framework 5.3+) to profile exactly which beans/phases take the most time. Mitigations: trim unused dependencies, use `@Lazy` selectively, or consider GraalVM native images for drastically reduced startup for latency-sensitive contexts (serverless).

**Q106. Why should you avoid `spring.jpa.hibernate.ddl-auto=update` in production?**
Because Hibernate's automatic schema inference can make unexpected, potentially destructive structural changes (dropping columns it thinks are unused, altering types incorrectly) without human review, and provides no audit trail/rollback capability — production schema changes should go through reviewed, versioned migration scripts (Flyway/Liquibase) instead.

**Q107. What is a common mistake with connection pool sizing in Spring Boot applications under load?**
Setting the HikariCP pool size (`spring.datasource.hikari.maximum-pool-size`) too high, assuming "more connections = more throughput" — in reality, past a certain point (related to available CPU cores and the database's own connection-handling capacity), additional pooled connections add contention overhead rather than improving throughput; HikariCP's own documentation recommends a formula based roughly on `((core_count * 2) + effective_spindle_count)` as a starting point, tuned empirically under realistic load testing.

**Q108. Why is `spring.jpa.open-in-view=false` often recommended for production APIs, despite being enabled by default?**
As discussed in Q60, OSIV holds a database connection open for the entire request duration (including template rendering / serialization, not just the actual data-access portion), which under high concurrent load can exhaust the connection pool faster than necessary, and it can silently mask N+1 query problems that would otherwise surface loudly (as exceptions) during development — explicit, scoped transactions in the service layer are considered a more scalable, deliberate architecture.

**Q109. What is a common pitfall when using `@Value` or `@ConfigurationProperties` with sensitive configuration and version control?**
Accidentally committing `application.yml` containing real credentials/secrets to a public or shared repository. Best practice: use placeholder/example files (`application-example.yml`) checked into source control, inject actual secrets via environment variables or a secrets manager at deploy time, and add real config files with secrets to `.gitignore`.

**Q110. What should you check first when a Spring Boot application fails to start with a `BeanCreationException`?**
Read the **exception chain from the bottom up** — Spring's exception messages often wrap the true root cause in several layers of `BeanCreationException`s naming which bean/configuration class failed; the actual underlying cause (e.g., a missing property, a database connection failure, a circular dependency, a missing `@Bean` dependency) is usually at the very end of the stack trace/cause chain, not the top-level message.

**Q111. Why should health checks used for Kubernetes liveness probes generally avoid checking external dependency status (e.g., database connectivity)?**
A liveness probe determines whether Kubernetes should **restart the container** — if liveness incorrectly includes external dependency checks (e.g., a downstream database being temporarily unavailable), Kubernetes may repeatedly restart a perfectly healthy application instance in a crash-loop, even though restarting it does nothing to fix the actual external outage. External dependency checks are more appropriate for the **readiness** probe (which controls whether traffic is routed to the instance, not whether it should be killed) — this is exactly why Spring Boot separates liveness and readiness health groups.

**Q112. What is a good general checklist for taking a Spring Boot application to production?**
Disable `ddl-auto=update` in favor of migrations; set `spring.jpa.open-in-view=false`; secure and limit exposed Actuator endpoints; configure structured logging with correlation IDs; set appropriate connection pool sizes based on load testing; configure graceful shutdown for zero-downtime deploys; externalize all environment-specific configuration (no hardcoded secrets); set up health checks appropriately split between liveness/readiness; enable metrics export to a monitoring backend (Prometheus/Datadog); and load/performance test before go-live.
