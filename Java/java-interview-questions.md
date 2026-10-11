# Java Interview Questions & Answers — Complete Guide

A comprehensive reference covering Core Java, OOP, Collections, Concurrency, Modern Java (8–21+), JVM internals, and best practices.

---

## Table of Contents
1. [Core Java Basics](#1-core-java-basics)
2. [OOP Concepts](#2-oop-concepts)
3. [Data Types, Variables & Operators](#3-data-types-variables--operators)
4. [Strings](#4-strings)
5. [Exception Handling](#5-exception-handling)
6. [Collections Framework](#6-collections-framework)
7. [Generics](#7-generics)
8. [Multithreading & Concurrency](#8-multithreading--concurrency)
9. [Java 8+ Features](#9-java-8-features)
10. [JVM Internals & Memory Management](#10-jvm-internals--memory-management)
11. [Garbage Collection](#11-garbage-collection)
12. [I/O and NIO](#12-io-and-nio)
13. [Serialization](#13-serialization)
14. [Reflection & Annotations](#14-reflection--annotations)
15. [Design Patterns in Java](#15-design-patterns-in-java)
16. [JDBC & Database Access](#16-jdbc--database-access)
17. [Modules (JPMS)](#17-modules-jpms)
18. [Modern Java Features (9–21+)](#18-modern-java-features-9-21)
19. [Testing (JUnit & Mockito)](#19-testing-junit--mockito)
20. [Best Practices & Common Pitfalls](#20-best-practices--common-pitfalls)

---

## 1. Core Java Basics

**Q1. What is Java, and what are its key features?**
Java is a class-based, object-oriented, platform-independent programming language. Key features: platform independence (via bytecode + JVM), automatic memory management (garbage collection), strong static typing, multithreading support, robust standard library, security model (no direct pointer access), and "write once, run anywhere" (WORA).

**Q2. What is the difference between JDK, JRE, and JVM?**
- **JVM (Java Virtual Machine):** Runs bytecode; provides the runtime environment (class loading, execution, memory management).
- **JRE (Java Runtime Environment):** JVM + core libraries needed to *run* Java applications.
- **JDK (Java Development Kit):** JRE + development tools (compiler `javac`, debugger, `jar`, etc.) needed to *build* Java applications.

**Q3. Why is Java called "platform independent"?**
Java source code is compiled to bytecode (`.class` files), not native machine code. Bytecode runs on any device with a compatible JVM, so the same compiled artifact runs on Windows, Linux, macOS, etc.

**Q4. What is the difference between compile-time and runtime?**
Compile-time errors are caught by the compiler (syntax, type mismatches) before execution. Runtime errors/exceptions occur while the program is executing (e.g., `NullPointerException`, `ArrayIndexOutOfBoundsException`).

**Q5. What is the `main` method signature, and why is it structured that way?**
```java
public static void main(String[] args)
```
- `public`: JVM can call it from outside the class.
- `static`: called without instantiating the class.
- `void`: returns nothing to the JVM.
- `String[] args`: command-line arguments.

**Q6. What is the difference between `==` and `.equals()`?**
`==` compares references (memory addresses) for objects, or actual values for primitives. `.equals()` compares logical/content equality, and its behavior depends on whether the class overrides it (default `Object.equals()` is reference equality).

**Q7. What is autoboxing and unboxing?**
Autoboxing is the automatic conversion of a primitive to its wrapper class (e.g., `int` → `Integer`). Unboxing is the reverse. The compiler inserts these conversions automatically, e.g., `Integer i = 5;` (boxing) and `int j = i;` (unboxing).

**Q8. What are wrapper classes?**
Classes that wrap primitive types into objects: `Integer`, `Long`, `Double`, `Float`, `Character`, `Boolean`, `Byte`, `Short`. Needed for use in collections (which require objects), and provide utility methods (parsing, conversion).

---

## 2. OOP Concepts

**Q9. What are the four pillars of OOP?**
1. **Encapsulation** — bundling data and methods, restricting direct access via access modifiers/getters-setters.
2. **Inheritance** — a class acquiring properties/behavior of another via `extends`.
3. **Polymorphism** — same interface, different implementations (method overloading = compile-time, overriding = runtime).
4. **Abstraction** — hiding implementation details, exposing only essential features (via abstract classes/interfaces).

**Q10. What is the difference between method overloading and overriding?**
- **Overloading:** Same method name, different parameter list, within the same class. Resolved at compile-time (static polymorphism).
- **Overriding:** Subclass provides a specific implementation of a method already defined in its superclass, with the same signature. Resolved at runtime (dynamic polymorphism), enabled by dynamic method dispatch.

**Q11. What is the difference between an abstract class and an interface?**
| Aspect | Abstract Class | Interface |
|---|---|---|
| Methods | Can have abstract + concrete methods | Traditionally abstract only; since Java 8 can have `default`/`static` methods |
| Fields | Any type of field | `public static final` (constants) only |
| Inheritance | Single inheritance (`extends`) | Multiple inheritance (`implements`) |
| Constructors | Can have constructors | Cannot have constructors |
| Access modifiers | Any | Public (implicitly) |

Use an abstract class for shared state/behavior among closely related classes; use an interface to define a contract/capability across unrelated classes.

**Q12. Why doesn't Java support multiple inheritance of classes?**
To avoid the **diamond problem** (ambiguity when two parent classes have a method with the same signature). Java allows multiple inheritance of *type* via interfaces, but default method conflicts must be explicitly resolved by the implementing class.

**Q13. What is constructor chaining?**
Calling one constructor from another within the same class (`this(...)`) or from a subclass to superclass (`super(...)`). Ensures proper initialization order — every constructor call chain ultimately reaches `Object`'s constructor.

**Q14. What is the `super` keyword used for?**
Refers to the immediate parent class object. Used to: call the parent constructor (`super(...)`), access parent class methods overridden in the subclass, and access parent class fields hidden by subclass fields.

**Q15. What is polymorphism, and how is it achieved in Java?**
The ability of an object to take many forms. Achieved via:
- **Compile-time (static)**: method overloading.
- **Runtime (dynamic)**: method overriding, achieved through **dynamic method dispatch** — the JVM determines which overridden method to call based on the actual object type at runtime, not the reference type.

**Q16. What is encapsulation, and why is it important?**
Wrapping data (fields) and methods into a single unit (class), restricting direct access to fields using `private` and exposing controlled access via public getters/setters. Improves maintainability, allows validation logic, and hides internal representation from external code.

**Q17. Can a constructor be private? Why would you do that?**
Yes. Common uses: **Singleton pattern** (prevent external instantiation), utility classes with only static members (prevent instantiation entirely), and static factory methods (force object creation through a named method).

**Q18. What is the difference between composition and inheritance?**
Inheritance models an "is-a" relationship (subclass is a type of superclass). Composition models a "has-a" relationship (a class contains an instance of another class as a field). Composition is generally favored over inheritance because it's more flexible and avoids tight coupling ("favor composition over inheritance").

**Q19. What is the `instanceof` operator?**
Checks whether an object is an instance of a specific class/interface. Since Java 16, supports **pattern matching**: `if (obj instanceof String s) { ... }` — binds and casts in one step.

---

## 3. Data Types, Variables & Operators

**Q20. What are Java's primitive data types?**
`byte` (8-bit), `short` (16-bit), `int` (32-bit), `long` (64-bit), `float` (32-bit), `double` (64-bit), `char` (16-bit, Unicode), `boolean` (true/false, size JVM-dependent).

**Q21. What is the default value of variables?**
Instance/static variables get default values (`0`, `0.0`, `false`, `null` for objects, `'\u0000'` for char) if not initialized. **Local variables have no default value** — the compiler requires explicit initialization before use.

**Q22. What is the difference between `final`, `finally`, and `finalize()`?**
- `final`: keyword — makes a variable constant, a method non-overridable, or a class non-inheritable.
- `finally`: block that always executes after try/catch, regardless of exception outcome (used for cleanup).
- `finalize()`: a deprecated `Object` method the GC used to call before reclaiming an object (removed in newer versions in favor of `try-with-resources`/`Cleaner`).

**Q23. What is the difference between stack and heap memory?**
- **Stack:** stores method call frames, local variables, and references; LIFO; memory is automatically reclaimed when a method returns; thread-specific.
- **Heap:** stores all objects and instance data; shared across threads; managed by the Garbage Collector.

**Q24. What is variable shadowing?**
When a variable declared in an inner scope (e.g., method parameter, local variable) has the same name as one in an outer scope (e.g., instance field), the inner variable "shadows" the outer one within that scope. Resolved using `this.fieldName` to access the shadowed instance field.

**Q25. What is the difference between `int` and `Integer`?**
`int` is a primitive (stored directly, stack-allocated for locals, cannot be `null`). `Integer` is a wrapper class (object, heap-allocated, can be `null`, has methods, used in generics/collections). `Integer` also has an internal cache for values -128 to 127 (`Integer.valueOf` caching), which affects `==` comparisons.

**Q26. What are var-args?**
Allows a method to accept a variable number of arguments of a specified type: `void method(String... args)`. Internally treated as an array. Must be the last parameter in the method signature.

---

## 4. Strings

**Q27. Why is String immutable in Java?**
Once created, a `String`'s internal character data cannot change. Reasons: **security** (used for class loading, file paths, network connections — immutability prevents tampering), **thread-safety** (safely shared across threads without synchronization), **String pool** caching relies on immutability, and it enables safe use as `HashMap` keys (hashcode can be cached).

**Q28. What is the String pool (String intern pool)?**
A special memory region in the heap where string literals are stored. When you write `String s = "hello";`, the JVM checks the pool first; if the literal exists, it returns the reference, otherwise it creates and caches it. `new String("hello")` bypasses the pool, creating a new object on the heap. `.intern()` forces a string into the pool.

**Q29. What is the difference between `String`, `StringBuilder`, and `StringBuffer`?**
| | Mutable? | Thread-safe? | Performance |
|---|---|---|---|
| `String` | No | Yes (immutable) | Slow for repeated modification |
| `StringBuilder` | Yes | No | Fast |
| `StringBuffer` | Yes | Yes (synchronized) | Slower than `StringBuilder` due to sync overhead |

Use `StringBuilder` for single-threaded string building; `StringBuffer` when thread-safety is required.

**Q30. How does `String` override `equals()` and `hashCode()`?**
`String.equals()` compares character sequences for content equality. `hashCode()` is computed from characters using the formula `s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]`, ensuring equal strings always produce equal hash codes (contract required for use in hash-based collections).

**Q31. What's the difference between `String.format()` and concatenation with `+`?**
`+` compiles (in modern Java) to `StringBuilder.append()` calls or `invokedynamic` with `StringConcatFactory`. `String.format()` uses a format string with placeholders (`%s`, `%d`) for more readable, locale-aware formatting — useful for complex formatting, slightly slower for simple cases.

---

## 5. Exception Handling

**Q32. What is the exception hierarchy in Java?**
```
Throwable
├── Error (unrecoverable: OutOfMemoryError, StackOverflowError)
└── Exception
    ├── Checked Exceptions (IOException, SQLException) — must be declared/caught
    └── RuntimeException (Unchecked: NullPointerException, ArrayIndexOutOfBoundsException, IllegalArgumentException)
```

**Q33. What is the difference between checked and unchecked exceptions?**
**Checked exceptions** are checked at compile-time; the method must either handle them (`try-catch`) or declare them (`throws`). Represent recoverable conditions (e.g., file not found). **Unchecked exceptions** (subclasses of `RuntimeException`) aren't checked at compile-time; typically represent programming errors (e.g., null dereference, illegal argument).

**Q34. What is the difference between `throw` and `throws`?**
`throw` is used to actually raise/throw an exception instance inside code: `throw new IllegalArgumentException("bad")`. `throws` is used in a method signature to declare that the method might propagate certain checked exceptions: `void readFile() throws IOException`.

**Q35. What is try-with-resources?**
A try statement that declares one or more resources (implementing `AutoCloseable`), which are automatically closed at the end of the block, in reverse order of declaration — even if an exception occurs. Eliminates the need for manual `finally` cleanup.
```java
try (BufferedReader br = new BufferedReader(new FileReader("f.txt"))) {
    return br.readLine();
} // br.close() called automatically
```

**Q36. What happens if an exception is thrown in both `try` and `finally`?**
The exception from `finally` **suppresses** the one from `try` — only the `finally` exception propagates (the original is lost unless you explicitly capture it via `Throwable.addSuppressed()`, as try-with-resources does automatically).

**Q37. Can you catch multiple exceptions in one catch block?**
Yes, using the pipe operator (multi-catch, Java 7+):
```java
catch (IOException | SQLException e) { ... }
```
The exception types must not be related by subclassing (no redundancy), and the resulting variable is implicitly `final`.

**Q38. What is exception chaining?**
Wrapping a lower-level exception inside a higher-level one to preserve the root cause while providing more context, using the constructor `new HigherLevelException("message", originalException)`. Retrievable via `getCause()`.

**Q39. What's the difference between `Error` and `Exception`?**
`Error` represents serious problems an application shouldn't try to catch/handle (e.g., `OutOfMemoryError`, `StackOverflowError`) — usually JVM-level issues. `Exception` represents conditions an application might want to catch and handle.

**Q40. Is it a good practice to catch `Exception` or `Throwable` generically?**
Generally no — it can mask bugs, catch unintended exceptions (including `Error`s), and hide the actual root cause. Prefer catching specific exception types; use generic catches only at top-level boundaries (e.g., a web framework's global error handler) with proper logging.

---

## 6. Collections Framework

**Q41. What is the Java Collections Framework?**
A unified architecture for representing and manipulating collections of objects, consisting of interfaces (`Collection`, `List`, `Set`, `Map`, `Queue`), implementations (`ArrayList`, `HashSet`, `HashMap`, etc.), and algorithms (via `Collections` utility class).

**Q42. What is the difference between `List`, `Set`, and `Map`?**
- **List:** ordered, allows duplicates, indexed access (`ArrayList`, `LinkedList`).
- **Set:** no duplicates, may or may not maintain order (`HashSet`, `LinkedHashSet`, `TreeSet`).
- **Map:** key-value pairs, unique keys (`HashMap`, `LinkedHashMap`, `TreeMap`). Note: `Map` doesn't extend `Collection`.

**Q43. What is the difference between `ArrayList` and `LinkedList`?**
- **ArrayList:** backed by a dynamic array; O(1) random access (`get(index)`); O(n) insertion/deletion in the middle (shifting elements).
- **LinkedList:** doubly-linked list; O(1) insertion/deletion at ends; O(n) random access; implements `Deque`, so usable as a queue/stack.

**Q44. How does `HashMap` work internally?**
`HashMap` stores entries in an array of buckets. The key's `hashCode()` is used (with additional bit-spreading) to determine the bucket index. Each bucket holds a linked list of entries (for collision handling); since Java 8, if a bucket's chain exceeds a threshold (8) and the table is large enough, it converts to a **red-black tree** for O(log n) worst-case lookup instead of O(n). Resizing (rehashing) occurs when the load factor (default 0.75) is exceeded, doubling capacity.

**Q45. What is the difference between `HashMap`, `LinkedHashMap`, and `TreeMap`?**
- **HashMap:** no ordering guarantee; O(1) average operations.
- **LinkedHashMap:** maintains insertion order (or access order if configured); O(1) operations with slightly more overhead.
- **TreeMap:** sorted by key (natural ordering or a `Comparator`); backed by a red-black tree; O(log n) operations.

**Q46. What is the difference between `HashMap` and `Hashtable`?**
`Hashtable` is legacy, synchronized (thread-safe but slow), and doesn't allow `null` keys/values. `HashMap` is unsynchronized (faster, not thread-safe), allows one `null` key and multiple `null` values. For concurrent use, prefer `ConcurrentHashMap` over `Hashtable`.

**Q47. What is the contract between `equals()` and `hashCode()`?**
If two objects are equal per `equals()`, they **must** have the same `hashCode()`. The reverse isn't required (unequal objects can share a hash code — a collision). Violating this contract breaks hash-based collections (`HashMap`, `HashSet`) — e.g., an object might become "lost" (unfindable) after being stored.

**Q48. What is `ConcurrentModificationException`, and how do you avoid it?**
Thrown when a collection is structurally modified (add/remove) while being iterated with a standard iterator (not through the iterator's own `remove()`), detected via a `modCount` check (fail-fast iterators). Avoid it by using `Iterator.remove()`, `CopyOnWriteArrayList`, `ConcurrentHashMap`, or collecting removals separately and applying them after iteration (or using `removeIf()`).

**Q49. What is the difference between `Comparable` and `Comparator`?**
- **Comparable** (`compareTo`): defines a class's *natural ordering*; implemented by the class itself; only one ordering possible.
- **Comparator** (`compare`): defines an *external, custom ordering*; can create multiple different orderings for the same class without modifying it; often used as a lambda: `list.sort((a, b) -> a.getName().compareTo(b.getName()))`.

**Q50. What is `Iterator` vs `ListIterator`?**
`Iterator` allows forward-only traversal and element removal. `ListIterator` (for `List` only) supports bidirectional traversal, element replacement (`set()`), addition (`add()`), and retrieving the current index.

**Q51. What is fail-fast vs fail-safe iteration?**
**Fail-fast** iterators (`ArrayList`, `HashMap`) throw `ConcurrentModificationException` if the collection is structurally modified during iteration, by checking a `modCount`. **Fail-safe** iterators (`CopyOnWriteArrayList`, `ConcurrentHashMap`) operate on a clone or a snapshot/weakly-consistent view, so they don't throw, but may not reflect the latest modifications.

**Q52. How do you make a collection immutable?**
`Collections.unmodifiableList(list)` wraps a collection (still backed by the original — mutations to the original are visible). `List.of(...)`, `Map.of(...)`, `Set.of(...)` (Java 9+) create truly immutable collections that throw `UnsupportedOperationException` on modification attempts.

**Q53. What is the difference between `Queue` and `Deque`?**
`Queue` supports FIFO operations (`offer`, `poll`, `peek`). `Deque` (double-ended queue) supports insertion/removal at both ends, so it can act as both a queue (FIFO) and a stack (LIFO) — `ArrayDeque` is preferred over the legacy `Stack` class for stack operations.

**Q54. What is `PriorityQueue`?**
A queue where elements are ordered by natural ordering or a supplied `Comparator`, backed by a binary heap. `poll()`/`peek()` always returns the smallest (or highest priority) element. Not thread-safe; O(log n) insertion/removal.

---

## 7. Generics

**Q55. What are generics, and why use them?**
Generics allow types (classes, interfaces, methods) to be parameterized, enabling compile-time type safety and eliminating the need for explicit casting. E.g., `List<String>` guarantees only `String`s can be added, catching type errors at compile time instead of `ClassCastException` at runtime.

**Q56. What is type erasure?**
Java implements generics via **type erasure**: generic type information exists only at compile-time for type-checking purposes; the compiler replaces type parameters with their bounds (or `Object` if unbounded) in the compiled bytecode, and inserts casts where necessary. This is why you can't do `new T()` or check `instanceof T` at runtime, and why generic type info isn't available via reflection at runtime.

**Q57. What are bounded type parameters?**
Restrict the types that can be used as a type argument: `<T extends Number>` restricts `T` to `Number` or its subtypes. Allows calling `Number`'s methods within the generic class/method.

**Q58. What is the difference between `<? extends T>` and `<? super T>` (wildcards)?**
- `<? extends T>` (upper bounded): accepts `T` or any subtype — read-only ("producer"), used when you only *read* from the structure. Follows **PECS**: "Producer Extends".
- `<? super T>` (lower bounded): accepts `T` or any supertype — write-friendly ("consumer"), used when you *write* to the structure. "Consumer Super".

**Q59. Can you create a generic array in Java?**
Not directly (`new T[10]` is illegal) due to type erasure and array covariance concerns (arrays retain runtime type info, but erased generics don't, so the JVM couldn't enforce type safety). Workarounds: use `Object[]` internally with casting, or use `@SuppressWarnings("unchecked")`, or prefer `List<T>` over arrays.

---

## 8. Multithreading & Concurrency

**Q60. What is the difference between a process and a thread?**
A **process** is an independent execution unit with its own memory space. A **thread** is a lightweight unit of execution within a process, sharing the process's memory (heap) but having its own stack, program counter, and registers.

**Q61. What are the ways to create a thread in Java?**
1. Extend `Thread` and override `run()`.
2. Implement `Runnable` and pass it to a `Thread` (preferred — allows extending other classes, decouples task from execution mechanism).
3. Implement `Callable<V>` (can return a value and throw checked exceptions) and submit to an `ExecutorService`.
4. Using the `ExecutorService` / thread pool abstractions (recommended for production).

**Q62. What is the difference between `Runnable` and `Callable`?**
`Runnable.run()` returns `void` and cannot throw checked exceptions. `Callable<V>.call()` returns a value of type `V` and can throw checked exceptions. `Callable` is used with `ExecutorService.submit()`, returning a `Future<V>`.

**Q63. What is the `synchronized` keyword?**
Ensures mutual exclusion — only one thread can execute a synchronized block/method on a given monitor (lock) at a time, preventing race conditions. Can be applied to instance methods (locks on `this`), static methods (locks on the `Class` object), or blocks (locks on a specified object).

**Q64. What is a race condition?**
A situation where multiple threads access and modify shared data concurrently, and the outcome depends on the unpredictable timing/interleaving of their execution, potentially producing incorrect results. Prevented via synchronization, locks, or atomic variables.

**Q65. What is a deadlock, and how do you prevent it?**
A deadlock occurs when two or more threads are blocked forever, each waiting for a resource held by another. Prevention strategies: always acquire locks in a consistent global order, use timeouts (`tryLock()`), avoid nested locks where possible, or use higher-level concurrency utilities.

**Q66. What is the difference between `wait()`/`notify()` and `sleep()`?**
- `wait()`/`notify()`/`notifyAll()`: `Object` methods; must be called within a `synchronized` block; releases the held lock while waiting, allowing other threads to acquire it; used for inter-thread communication.
- `sleep()`: `Thread` static method; pauses the current thread without releasing any locks it holds; purely a timed pause.

**Q67. What is the `volatile` keyword?**
Ensures visibility — writes to a `volatile` variable by one thread are immediately visible to other threads, by preventing caching in CPU registers/thread-local caches and disallowing certain instruction reordering. It does **not** provide atomicity for compound operations (e.g., `count++` is still not atomic even if `count` is volatile).

**Q68. What is the Java Memory Model (JMM)?**
Defines how threads interact through memory — what visibility and ordering guarantees exist for reads/writes across threads. Establishes "happens-before" relationships (e.g., a write before a monitor unlock happens-before a subsequent read after the corresponding lock) that determine when changes made by one thread are guaranteed visible to another.

**Q69. What are atomic classes (`java.util.concurrent.atomic`)?**
Classes like `AtomicInteger`, `AtomicLong`, `AtomicReference` that provide lock-free, thread-safe operations on single variables using low-level CPU instructions (CAS — Compare-And-Swap), avoiding the overhead of synchronization for simple atomic updates.

**Q70. What is `ExecutorService`, and why use it over raw threads?**
A higher-level API for managing a pool of worker threads, decoupling task submission from thread management. Benefits: thread reuse (avoids the cost of thread creation per task), controlled resource usage (bounded pool size), and built-in task scheduling/lifecycle management (`submit`, `invokeAll`, `shutdown`).

**Q71. What are the common thread pool types in `Executors`?**
- `newFixedThreadPool(n)`: fixed number of threads.
- `newCachedThreadPool()`: creates threads as needed, reuses idle ones, unbounded — risk of resource exhaustion under heavy load.
- `newSingleThreadExecutor()`: single worker thread, tasks run sequentially.
- `newScheduledThreadPool(n)`: supports delayed/periodic task execution.
(Note: Since Java 9+ many teams prefer configuring `ThreadPoolExecutor` directly for production, to avoid the unbounded queue/thread risks of some `Executors` factory methods.)

**Q72. What is `CompletableFuture`?**
A `Future` implementation (Java 8+) supporting asynchronous, non-blocking composition of tasks — chaining (`thenApply`, `thenCompose`), combining multiple futures (`thenCombine`, `allOf`), and exception handling (`exceptionally`, `handle`), enabling readable async pipelines without manual callback nesting.

**Q73. What is `ConcurrentHashMap`, and how does it achieve thread-safety without locking the whole map?**
A thread-safe `Map` implementation that (in modern versions) uses fine-grained locking at the bucket/node level (synchronized blocks on individual bins) combined with CAS operations, rather than locking the entire map — allowing much higher concurrency than a synchronized `HashMap`. Iterators are weakly consistent (fail-safe, not fail-fast).

**Q74. What is the difference between `synchronized` and `ReentrantLock`?**
`ReentrantLock` (from `java.util.concurrent.locks`) offers more flexibility than `synchronized`: try-lock with timeout (`tryLock()`), interruptible lock acquisition, fairness policies, and multiple condition variables (`Condition`) per lock. `synchronized` is simpler and automatically releases the lock (even on exceptions), while `ReentrantLock` requires manual `unlock()` in a `finally` block.

**Q75. What is thread starvation and thread livelock?**
**Starvation:** a thread is perpetually denied access to a resource because other threads are repeatedly favored (e.g., unfair scheduling). **Livelock:** threads are actively responding to each other (not blocked) but make no real progress — e.g., two threads repeatedly yielding to each other.

---

## 9. Java 8+ Features

**Q76. What are lambda expressions?**
A concise way to represent an anonymous function — an implementation of a functional interface — without boilerplate. Syntax: `(parameters) -> expression` or `(parameters) -> { statements }`. E.g., `Runnable r = () -> System.out.println("run");`.

**Q77. What is a functional interface?**
An interface with exactly one abstract method (may have any number of `default`/`static` methods), enabling it to be implemented via a lambda expression. Marked (optionally) with `@FunctionalInterface` for compile-time verification. Examples: `Runnable`, `Comparator<T>`, `Function<T,R>`, `Predicate<T>`, `Supplier<T>`, `Consumer<T>`.

**Q78. What are the core functional interfaces in `java.util.function`?**
- `Function<T,R>`: takes `T`, returns `R` (`apply`).
- `Predicate<T>`: takes `T`, returns `boolean` (`test`).
- `Consumer<T>`: takes `T`, returns nothing (`accept`).
- `Supplier<T>`: takes nothing, returns `T` (`get`).
- `BiFunction<T,U,R>`, `UnaryOperator<T>`, `BinaryOperator<T>`: variations for multiple arguments/same-type operations.

**Q79. What is the Stream API?**
An abstraction (Java 8+) for processing sequences of elements in a functional, declarative style, supporting operations like `filter`, `map`, `reduce`, `sorted`, `collect`. Streams are **lazy** (intermediate operations don't execute until a terminal operation is invoked) and typically **not reusable** (consumed once).

**Q80. What is the difference between intermediate and terminal stream operations?**
**Intermediate operations** (`filter`, `map`, `sorted`, `distinct`) return a new `Stream` and are lazily evaluated. **Terminal operations** (`collect`, `forEach`, `reduce`, `count`) trigger the actual processing and produce a result or side-effect, after which the stream is considered consumed.

**Q81. What is the difference between `map()` and `flatMap()`?**
`map()` transforms each element into another single element (1-to-1), e.g., `String → Integer`. `flatMap()` transforms each element into a stream and flattens all resulting streams into a single stream (1-to-many), e.g., flattening a `List<List<String>>` into a single `Stream<String>`.

**Q82. What is `Optional`, and why was it introduced?**
A container object (Java 8+) that may or may not hold a non-null value, used as a return type to explicitly signal "a value might be absent" — reducing `NullPointerException`s and forcing callers to handle the empty case explicitly (`isPresent()`, `orElse()`, `orElseThrow()`, `map()`). Not intended for use as a field type or method parameter.

**Q83. What are default and static methods in interfaces?**
Introduced in Java 8 to allow adding new methods to interfaces without breaking existing implementations. **Default methods** (`default void method() {...}`) provide a body that implementing classes inherit unless overridden. **Static methods** belong to the interface itself and aren't inherited by implementers, called as `InterfaceName.method()`.

**Q84. What is method reference syntax?**
A shorthand for lambdas that just call an existing method: `ClassName::methodName` (static), `object::instanceMethod`, `ClassName::instanceMethod` (unbound), `ClassName::new` (constructor reference). E.g., `list.forEach(System.out::println)`.

**Q85. What is the new Date/Time API (`java.time`)?**
Introduced in Java 8 to replace the flawed, mutable `Date`/`Calendar` classes. Key classes: `LocalDate`, `LocalTime`, `LocalDateTime` (no timezone), `ZonedDateTime` (with timezone), `Duration`/`Period` (time spans), `Instant` (machine timestamp). All classes are **immutable** and thread-safe.

**Q86. How do you collect a stream into a `Map` or grouped structure?**
Using `Collectors`:
```java
Map<String, List<Person>> byCity = people.stream()
    .collect(Collectors.groupingBy(Person::getCity));

Map<String, Integer> nameToAge = people.stream()
    .collect(Collectors.toMap(Person::getName, Person::getAge));
```
`Collectors.partitioningBy` splits into two groups (true/false); `Collectors.joining` concatenates strings.

**Q87. What is a parallel stream, and when should you avoid it?**
`stream.parallel()` splits the workload across multiple threads (via the common `ForkJoinPool`) to potentially process elements concurrently. Avoid it for: small datasets (overhead outweighs benefit), I/O-bound tasks, operations with shared mutable state, or when order matters and hasn't been explicitly preserved — always benchmark before adopting.

---

## 10. JVM Internals & Memory Management

**Q88. What are the main components of the JVM?**
- **Class Loader Subsystem:** loads, links, and initializes classes.
- **Runtime Data Areas:** Method Area, Heap, Stack, PC Registers, Native Method Stacks.
- **Execution Engine:** interpreter, JIT (Just-In-Time) compiler, garbage collector.
- **Native Method Interface (JNI):** bridges to native (C/C++) libraries.

**Q89. What are the different memory areas in the JVM?**
- **Heap:** stores all objects; shared across threads; divided into Young Generation (Eden + Survivor spaces) and Old Generation.
- **Stack:** per-thread; stores frames for method calls (local variables, partial results).
- **Method Area (Metaspace since Java 8):** stores class metadata, static variables, constant pool.
- **PC Register:** per-thread; holds the address of the currently executing instruction.
- **Native Method Stack:** for native (non-Java) method calls.

**Q90. What is the difference between the Java 7 PermGen and Java 8+ Metaspace?**
**PermGen** was a fixed-size heap region storing class metadata, often causing `OutOfMemoryError: PermGen space` in apps with heavy classloading (e.g., app servers). **Metaspace** (Java 8+) replaced it, allocated from **native (off-heap) memory**, growing dynamically by default — significantly reducing such OOM errors, though it can still be bounded via `-XX:MaxMetaspaceSize`.

**Q91. What are the phases of class loading?**
1. **Loading:** reading the `.class` file bytecode into memory.
2. **Linking:** Verification (bytecode validity/security) → Preparation (allocate memory for static fields with default values) → Resolution (resolve symbolic references to actual references).
3. **Initialization:** execute static initializers and assign values to static fields.

**Q92. What is the class loader hierarchy?**
- **Bootstrap ClassLoader:** loads core JDK classes (`java.lang.*`), written in native code.
- **Platform/Extension ClassLoader:** loads classes from the extensions mechanism.
- **Application (System) ClassLoader:** loads classes from the application classpath.
Follows a **delegation model** — a class loader delegates to its parent first before attempting to load a class itself, ensuring core classes can't be overridden and are loaded only once.

**Q93. What is JIT (Just-In-Time) compilation?**
The execution engine initially interprets bytecode, but the JIT compiler identifies "hot" (frequently executed) code paths and compiles them into native machine code at runtime, caching the result for faster subsequent execution — balancing startup speed (interpretation) with peak performance (native compilation).

**Q94. What causes a `StackOverflowError`?**
Excessive stack depth, typically from uncontrolled/infinite recursion (missing or incorrect base case), exhausting the thread's stack space allocated for method call frames.

**Q95. What causes an `OutOfMemoryError`, and what are its common variants?**
Occurs when the JVM cannot allocate needed memory. Variants: `Java heap space` (too many live objects, memory leak, or heap too small), `Metaspace` (excessive class loading, e.g., dynamic proxy generation), `GC overhead limit exceeded` (GC running excessively without freeing much memory), `Unable to create new native thread` (OS thread limit reached).

---

## 11. Garbage Collection

**Q96. What is Garbage Collection (GC)?**
The automatic process by which the JVM identifies and reclaims memory occupied by objects that are no longer reachable from any live thread/root reference, freeing developers from manual memory deallocation and preventing certain classes of memory leaks/dangling pointers.

**Q97. How does the JVM determine an object is eligible for garbage collection?**
Via **reachability analysis** starting from GC roots (local variables on active thread stacks, static fields, JNI references, active threads). An object unreachable from any GC root is considered garbage, regardless of reference count.

**Q98. What is the generational hypothesis, and how does it shape heap design?**
Most objects "die young" (are short-lived), while a small fraction survive for a long time. The heap is therefore divided into a **Young Generation** (Eden + two Survivor spaces, collected frequently with fast "minor GC") and an **Old Generation** (collected less frequently with more expensive "major/full GC"), improving overall GC efficiency.

**Q99. What are the common garbage collectors in the JVM?**
- **Serial GC:** single-threaded, suited for small applications.
- **Parallel GC:** multi-threaded, throughput-focused (default in older Java versions).
- **CMS (Concurrent Mark Sweep):** low-pause, deprecated/removed in newer versions.
- **G1 (Garbage First):** region-based, balances throughput and pause time (default since Java 9).
- **ZGC / Shenandoah:** ultra-low-latency collectors designed for very large heaps with sub-millisecond pause targets (Java 11+/12+).

**Q100. What are strong, weak, soft, and phantom references?**
- **Strong reference:** normal reference; prevents GC as long as it exists.
- **Soft reference:** collected only when the JVM is low on memory (good for memory-sensitive caches).
- **Weak reference:** collected at the next GC cycle if no strong references exist (used in `WeakHashMap`).
- **Phantom reference:** doesn't prevent collection; enqueued after the object is finalized, used for post-mortem cleanup actions (via `ReferenceQueue`), replacing the deprecated `finalize()`.

**Q101. What is a memory leak in Java, given it has automatic GC?**
A memory leak occurs when objects are no longer needed but remain reachable (e.g., via static collections that grow unbounded, unclosed resources, listeners not deregistered, inner class references keeping outer objects alive), preventing the GC from reclaiming them, eventually leading to `OutOfMemoryError`.

---

## 12. I/O and NIO

**Q102. What is the difference between `java.io` and `java.nio`?**
- **java.io (traditional I/O):** stream-based (`InputStream`/`OutputStream`, `Reader`/`Writer`), blocking, processes data sequentially byte/char by byte/char.
- **java.nio (New I/O, since Java 1.4/7):** buffer-based (`ByteBuffer`), channel-based (`FileChannel`, `SocketChannel`), supports non-blocking I/O and **selectors** (multiplexing many channels with one thread) — better suited for high-throughput, scalable I/O.

**Q103. What is the difference between `InputStream`/`OutputStream` and `Reader`/`Writer`?**
`InputStream`/`OutputStream` handle raw **byte** data (binary files, images). `Reader`/`Writer` handle **character** data, with encoding/decoding handled automatically (e.g., `InputStreamReader` bridges bytes to chars using a specified charset) — appropriate for text files.

**Q104. What is buffering, and why does it matter for I/O performance?**
Wrapping a stream with a buffered variant (`BufferedInputStream`, `BufferedReader`) batches reads/writes in memory, drastically reducing the number of expensive I/O system calls compared to unbuffered, byte-by-byte access.

**Q105. What is `NIO.2` (Java 7's `java.nio.file` package)?**
Introduced the `Path` interface (replacing much of `File`'s use), `Files` utility class (rich file operations: copy, move, walk directory trees), and `WatchService` (monitoring filesystem changes) — a more modern, exception-informative, and symbolic-link-aware file API.

---

## 13. Serialization

**Q106. What is serialization and deserialization?**
**Serialization** converts an object's state into a byte stream (for storage or transmission). **Deserialization** reconstructs the object from that byte stream. Achieved by implementing the marker interface `Serializable`.

**Q107. What is `serialVersionUID`, and why is it important?**
A unique identifier for a `Serializable` class's version, used during deserialization to verify that the sender and receiver of a serialized object have compatible class versions. If not declared explicitly, the JVM generates one automatically based on class details — which can cause `InvalidClassException` if the class changes slightly between serialization and deserialization. Best practice: declare it explicitly.

**Q108. What does the `transient` keyword do?**
Marks a field to be **excluded** from the default serialization process (e.g., for sensitive data like passwords, or non-serializable fields like `Thread`/socket handles). Transient fields are reset to their default value upon deserialization.

**Q109. What are the drawbacks of Java's built-in serialization?**
Security risks (deserialization of untrusted data can lead to remote code execution — a notorious vulnerability class), poor performance/verbose format compared to alternatives, versioning fragility, and lack of language interoperability. Modern systems often prefer JSON (Jackson/Gson), Protocol Buffers, or Avro instead.

---

## 14. Reflection & Annotations

**Q110. What is reflection in Java?**
The ability to inspect and manipulate classes, methods, fields, and constructors at runtime, even ones not known at compile-time — via the `java.lang.reflect` package and `Class` objects. Used heavily by frameworks (Spring, Hibernate, JUnit) for dependency injection, ORM mapping, and test discovery.

**Q111. What are the pros and cons of reflection?**
**Pros:** enables generic frameworks, dynamic behavior, introspection tools (debuggers, IDEs). **Cons:** performance overhead (bypasses JIT optimizations), breaks encapsulation (can access private members via `setAccessible(true)`), loses compile-time type safety, and can make code harder to understand/maintain.

**Q112. What are annotations, and what are the built-in ones?**
Metadata attached to code elements (classes, methods, fields) that don't directly affect execution but can be processed by the compiler, tools, or at runtime via reflection. Built-in: `@Override`, `@Deprecated`, `@SuppressWarnings`, `@FunctionalInterface`, `@SafeVarargs`.

**Q113. What are meta-annotations, and how do you create a custom annotation?**
Annotations that apply to other annotations: `@Retention` (SOURCE/CLASS/RUNTIME — controls how long the annotation is retained), `@Target` (which elements it can apply to), `@Documented`, `@Inherited`. Example:
```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface LogExecutionTime {}
```

---

## 15. Design Patterns in Java

**Q114. What is the Singleton pattern, and how do you implement it thread-safely?**
Ensures a class has only one instance, with a global access point. Thread-safe implementations:
- **Eager initialization:** instance created at class loading (simple, always instantiated).
- **Double-checked locking** with a `volatile` field (lazy + efficient).
- **Enum singleton:** `enum Singleton { INSTANCE; }` — simplest, inherently thread-safe, and serialization-safe (recommended by Joshua Bloch).
- **Initialization-on-demand holder idiom:** relies on class-loading guarantees for lazy, thread-safe init without synchronization overhead.

**Q115. What is the Factory pattern?**
Encapsulates object creation logic in a separate method/class, decoupling client code from concrete classes. A **Factory Method** lets subclasses decide which class to instantiate; an **Abstract Factory** provides an interface for creating families of related objects.

**Q116. What is the Builder pattern, and when is it useful?**
Separates the construction of a complex object from its representation, allowing step-by-step construction (often via method chaining). Useful when a class has many optional/constructor parameters, avoiding "telescoping constructors." Common in Java via fluent builders and `Lombok`'s `@Builder`.

**Q117. What is the Observer pattern?**
Defines a one-to-many dependency where a "subject" notifies registered "observers" of state changes automatically. Java historically provided `java.util.Observer`/`Observable` (now deprecated); modern implementations often use listener interfaces or reactive streams.

**Q118. What is Dependency Injection (DI), and how does it relate to the Strategy pattern / Inversion of Control?**
DI is a technique where an object's dependencies are provided (injected) externally rather than created internally, promoting loose coupling and testability. It's an implementation of the broader **Inversion of Control (IoC)** principle. Frameworks like Spring manage object lifecycles and wire dependencies automatically (via constructor, setter, or field injection).

**Q119. What is the Decorator pattern, and where is it used in the JDK?**
Attaches additional responsibilities to an object dynamically by wrapping it in another object implementing the same interface, without altering the original class. Classic JDK example: `java.io` stream wrapping — `new BufferedReader(new InputStreamReader(new FileInputStream("f.txt")))`.

---

## 16. JDBC & Database Access

**Q120. What is JDBC?**
Java Database Connectivity — a standard API for connecting to and executing queries against relational databases, using driver implementations specific to each database vendor.

**Q121. What are the main JDBC components/steps?**
1. Load/register the driver (auto-discovered via `ServiceLoader` since JDBC 4.0).
2. Establish a `Connection` via `DriverManager.getConnection(url, user, pass)`.
3. Create a `Statement`/`PreparedStatement`/`CallableStatement`.
4. Execute the query, obtaining a `ResultSet` (for SELECT) or update count.
5. Process results.
6. Close resources (ideally via try-with-resources).

**Q122. What is the difference between `Statement` and `PreparedStatement`?**
`Statement` executes static SQL directly; vulnerable to **SQL injection** if user input is concatenated into the query string. `PreparedStatement` uses parameterized queries (`?` placeholders) which are precompiled and bind parameters safely, protecting against SQL injection and offering better performance for repeated execution.

**Q123. What is connection pooling, and why is it used?**
Maintaining a reusable pool of open database connections (via libraries like HikariCP) instead of opening/closing a new connection per request — since connection creation is expensive. Improves performance and resource utilization under load.

**Q124. What is a transaction, and what does ACID mean?**
A transaction is a unit of work that must complete fully or not at all. **ACID**: **Atomicity** (all-or-nothing), **Consistency** (valid state transitions), **Isolation** (concurrent transactions don't interfere), **Durability** (committed changes persist). In JDBC, controlled via `connection.setAutoCommit(false)`, `commit()`, `rollback()`.

---

## 17. Modules (JPMS)

**Q125. What is the Java Platform Module System (JPMS), introduced in Java 9?**
A system for organizing code into **modules** — explicitly declared units with defined dependencies (`requires`) and exposed packages (`exports`), improving encapsulation (internal packages are hidden by default, unlike public classes on the classpath), reliable configuration (dependency conflicts detected at compile/launch time), and enabling smaller custom runtime images (`jlink`).

**Q126. What is `module-info.java`?**
A special file at a module's root declaring its name, dependencies, and exported packages:
```java
module com.example.app {
    requires java.sql;
    exports com.example.app.api;
}
```

---

## 18. Modern Java Features (9–21+)

**Q127. What is the `var` keyword (Java 10)?**
Enables **local variable type inference** — the compiler infers the type from the initializer expression. `var list = new ArrayList<String>();` — still statically typed (not dynamic typing); improves readability for verbose generic types, but should be avoided when it reduces clarity.

**Q128. What are records (Java 16)?**
A concise syntax for immutable data-carrier classes. The compiler automatically generates a canonical constructor, `private final` fields, accessors, `equals()`, `hashCode()`, and `toString()`:
```java
public record Point(int x, int y) {}
```
Records can implement interfaces and have additional methods/static members, but cannot extend other classes (implicitly extend `Record`) and cannot add instance fields beyond the declared components.

**Q129. What are sealed classes/interfaces (Java 17)?**
Restrict which classes/interfaces can extend or implement them, explicitly listed via `permits`:
```java
public sealed interface Shape permits Circle, Square, Triangle {}
```
Enables exhaustive pattern matching (the compiler knows all possible subtypes) and more controlled, intentional class hierarchies.

**Q130. What is pattern matching for `switch` (Java 21)?**
Extends `switch` to match on type patterns (and later record deconstruction patterns), with exhaustiveness checking for sealed types:
```java
String describe(Object obj) {
    return switch (obj) {
        case Integer i when i > 0 -> "positive int";
        case Integer i -> "non-positive int";
        case String s -> "string: " + s;
        default -> "unknown";
    };
}
```

**Q131. What are text blocks (Java 15)?**
Multi-line string literals using triple quotes (`"""`), preserving formatting without needing escape characters/concatenation for embedded newlines/quotes — useful for embedded SQL, JSON, or HTML.
```java
String json = """
    {
      "name": "Java"
    }
    """;
```

**Q132. What are virtual threads (Java 21, Project Loom)?**
Lightweight threads managed by the JVM (not the OS) that make blocking code scale to massive concurrency (millions of threads) without the memory/context-switch overhead of platform (OS) threads. Ideal for high-throughput I/O-bound server applications, allowing simple blocking-style code to achieve reactive-style scalability. Created via `Thread.ofVirtual()` or `Executors.newVirtualThreadPerTaskExecutor()`.

**Q133. What is the difference between local classes, anonymous classes, and lambdas?**
- **Local class:** a named class defined inside a method body.
- **Anonymous class:** an unnamed class defined and instantiated inline, typically implementing an interface or extending a class with an inline body — creates a real `.class` file and `this` refers to the anonymous instance.
- **Lambda:** a concise syntax specifically for implementing functional interfaces; doesn't create a separate class in the same way (compiled via `invokedynamic`), and `this` refers to the enclosing instance (lexical scoping).

---

## 19. Testing (JUnit & Mockito)

**Q134. What is the difference between JUnit 4 and JUnit 5?**
JUnit 5 (Jupiter) is modular (Platform + Jupiter + Vintage for backward compatibility), supports Java 8+ features (lambdas in assertions), nested tests (`@Nested`), parameterized tests (`@ParameterizedTest`), and a more extensible architecture (`@ExtendWith` replacing `@RunWith`/rules).

**Q135. What are common JUnit annotations?**
`@Test` (marks a test method), `@BeforeEach`/`@AfterEach` (run before/after each test), `@BeforeAll`/`@AfterAll` (run once for the class, must be static unless using `@TestInstance(PER_CLASS)`), `@Disabled` (skip a test), `@ParameterizedTest` with `@ValueSource`/`@CsvSource` for data-driven tests.

**Q136. What is mocking, and what is Mockito used for?**
Mocking creates fake implementations of dependencies to isolate the unit under test, so tests don't rely on real databases/network calls/other components. Mockito lets you create mock objects (`mock(Service.class)`), define behavior (`when(...).thenReturn(...)`), and verify interactions (`verify(mock).method()`).

**Q137. What is the difference between a mock, a stub, and a spy?**
- **Stub:** returns predefined answers to calls, no behavior verification.
- **Mock:** a stub that also records interactions, allowing verification of *how* it was used (call count, arguments).
- **Spy:** wraps a real object, allowing selective overriding of some methods while others execute their real implementation.

**Q138. What is Test-Driven Development (TDD)?**
A development approach where tests are written *before* the implementation, following the cycle: **Red** (write a failing test) → **Green** (write minimal code to pass) → **Refactor** (clean up while keeping tests green).

---

## 20. Best Practices & Common Pitfalls

**Q139. What is the difference between "pass by value" and "pass by reference" in Java?**
Java is **strictly pass-by-value**. For primitives, the value itself is copied. For objects, the *reference* (pointer to the object) is copied — so the method can mutate the object's internal state, but reassigning the parameter inside the method doesn't affect the caller's original reference.

**Q140. What is the difference between shallow copy and deep copy?**
A **shallow copy** duplicates an object but copies references to nested objects (both copies share the same nested objects — mutating one affects the other). A **deep copy** recursively duplicates nested objects as well, producing a fully independent copy.

**Q141. Why is it recommended to program to an interface, not an implementation?**
Decouples client code from specific implementations, allowing implementations to be swapped without changing dependent code, improving testability (mocking) and flexibility (e.g., declaring `List<String> list = new ArrayList<>();` rather than `ArrayList<String> list = ...`).

**Q142. What is the difference between `equals()` and `==` for wrapper classes, and what's a common bug?**
`Integer a = 127, b = 127; a == b` is `true` (cached, `Integer` cache covers -128 to 127). But `Integer a = 200, b = 200; a == b` is `false` (outside the cache range, different objects), a classic bug when developers assume `==` works for boxed integers as it does for primitives. Always use `.equals()` for wrapper class comparisons.

**Q143. What is immutability, and why is it recommended for classes where possible?**
An immutable object's state cannot change after construction. Benefits: inherently thread-safe (no synchronization needed), safe to share/cache freely, simpler to reason about (no unexpected side effects). To make a class immutable: make it `final`, all fields `private final`, no setters, defensively copy mutable fields in the constructor/getters.

**Q144. What is the difference between `clone()` and a copy constructor?**
`clone()` (via `Cloneable`) is notoriously error-prone (requires careful handling of `CloneNotSupportedException`, doesn't call constructors, easy to get shallow vs. deep copy wrong). A **copy constructor** (`new Point(otherPoint)`) or static factory copy method is generally preferred — clearer, more flexible, and doesn't rely on the flawed `Cloneable` contract.

**Q145. What are some common causes of memory leaks in long-running Java applications?**
Unbounded caches/static collections that keep growing, unclosed resources (streams, connections — mitigated by try-with-resources), listener/callback registrations without deregistration, `ThreadLocal` values not cleared in pooled-thread environments, and inner (non-static) classes unintentionally holding references to their outer class instance.

**Q146. What is the difference between `String.equals()` case sensitivity handling and best practice for comparisons?**
Use `.equalsIgnoreCase()` for case-insensitive comparison rather than manually lower/upper-casing both strings (avoids locale-related bugs, e.g., Turkish "I" issues, and is more efficient).

**Q147. Why should you override `toString()`, `equals()`, and `hashCode()` together?**
`toString()` aids debugging/logging by providing a meaningful string representation. `equals()`/`hashCode()` should always be overridden together (per the contract in Q47) to ensure correct behavior in hash-based collections and logical equality checks — many IDEs and libraries (Lombok, records) can generate these automatically.

**Q148. What is the "effectively final" rule for variables used in lambdas/anonymous classes?**
A local variable referenced inside a lambda or anonymous class must be `final` or "effectively final" (never reassigned after initialization) — because the lambda/anonymous class may outlive the method's stack frame, so the compiler captures a copy of the variable's value rather than allowing mutation.

**Q149. What are some JVM tuning flags commonly used in production?**
`-Xms`/`-Xmx` (initial/max heap size), `-XX:+UseG1GC` (or other GC selection), `-XX:MaxMetaspaceSize`, `-Xss` (thread stack size), `-XX:+HeapDumpOnOutOfMemoryError` (capture diagnostics), `-XX:+PrintGCDetails`/GC logging flags for monitoring collector behavior.

**Q150. What is the difference between horizontal and vertical scaling as it relates to Java application design, and how do virtual threads/reactive programming relate?**
**Vertical scaling** adds more resources (CPU/RAM) to a single instance; **horizontal scaling** adds more instances. Traditional blocking I/O with platform threads limits per-instance concurrency (thread-per-request doesn't scale to huge connection counts due to OS thread overhead). Reactive programming (e.g., Project Reactor) and virtual threads (Java 21) both address this — reactive via non-blocking event loops, virtual threads by making blocking-style code cheap to scale — letting a single instance handle far more concurrent work before horizontal scaling is needed.

---

*This guide covers Core Java through Java 21+. For deeper preparation, pair this with hands-on coding practice (data structures, algorithms) and framework-specific study (Spring, Hibernate) depending on the role.*
