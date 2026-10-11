# Java Collections Interview Questions & Answers

A comprehensive guide covering the Java Collections Framework from fundamentals through advanced internals — interfaces, List/Set/Map/Queue implementations, iteration, comparisons, concurrency, generics, and performance.

---

## Table of Contents

1. [Collections Framework Fundamentals](#1-collections-framework-fundamentals)
2. [The Collection Hierarchy & Core Interfaces](#2-the-collection-hierarchy--core-interfaces)
3. [List Implementations](#3-list-implementations)
4. [Set Implementations](#4-set-implementations)
5. [Map Implementations](#5-map-implementations)
6. [Queue & Deque Implementations](#6-queue--deque-implementations)
7. [Iterators & Iteration](#7-iterators--iteration)
8. [Comparable & Comparator](#8-comparable--comparator)
9. [`equals()`, `hashCode()` & Object Contracts](#9-equals-hashcode--object-contracts)
10. [Generics in Collections](#10-generics-in-collections)
11. [Collections Utility Class & Immutability](#11-collections-utility-class--immutability)
12. [Streams & Functional Operations on Collections](#12-streams--functional-operations-on-collections)
13. [Concurrent Collections](#13-concurrent-collections)
14. [Internal Implementation Details](#14-internal-implementation-details)
15. [Performance & Big-O Complexity](#15-performance--big-o-complexity)
16. [Common Pitfalls & Best Practices](#16-common-pitfalls--best-practices)
17. [Miscellaneous & Advanced Topics](#17-miscellaneous--advanced-topics)

---

## 1. Collections Framework Fundamentals

### Q1. What is the Java Collections Framework?
The Java Collections Framework (JCF) is a unified architecture (introduced in Java 1.2) of interfaces, implementations, and algorithms for storing and manipulating groups of objects. It provides standardized data structures (lists, sets, maps, queues) with consistent APIs, reducing the need for developers to write custom data structure implementations and enabling interoperability between different parts of a codebase or different libraries.

### Q2. What are the main benefits of using the Collections Framework over raw arrays?
- **Dynamic sizing** — collections grow/shrink automatically, unlike fixed-size arrays.
- **Rich, standardized API** — consistent methods for adding, removing, searching, sorting, and iterating across implementations.
- **Type safety** via generics, catching type errors at compile time.
- **Interoperability** — algorithms (`Collections.sort()`, etc.) work uniformly across any implementation of a given interface.
- **Reduced boilerplate** — no need to hand-write common data structures (linked lists, hash tables, balanced trees) from scratch.
- **Interchangeability** — code written against an interface (`List`, `Map`) can swap the underlying implementation with minimal changes.

### Q3. What are the four main "kinds" of collections in the framework?
- **List** — an ordered collection (sequence) that allows duplicate elements, accessible by index.
- **Set** — a collection that contains **no duplicate** elements.
- **Queue** — a collection designed for holding elements prior to processing, typically in a specific order (FIFO, priority-based).
- **Map** — an object that maps **keys to values**, with no duplicate keys allowed (technically not a true `Collection` subtype, but considered part of the framework).

### Q4. What is the difference between a `Collection` and a `Map`?
`Collection` (the root interface for List/Set/Queue) represents a group of individual **elements**. `Map` represents a group of **key-value pairs**, and does **not** extend `Collection` at all — it's a separate hierarchy, since its fundamental operations (associating a key with a value) don't fit the single-element-based `Collection` interface's contract. `Map` does provide collection **views** of itself (`keySet()`, `values()`, `entrySet()`) that do implement `Collection`/`Set`.

### Q5. What package are the core Collections Framework classes/interfaces located in?
`java.util` — including `List`, `Set`, `Map`, `Queue`, `ArrayList`, `HashMap`, `HashSet`, `LinkedList`, etc. Concurrent collection variants live in `java.util.concurrent` (and `java.util.concurrent.atomic` for related atomic utilities).

---

## 2. The Collection Hierarchy & Core Interfaces

### Q6. What is the root interface of the Collections Framework, and what does it define?
`Iterable<T>` is the ultimate root (defining `iterator()`, enabling the enhanced for-loop). `Collection<E>` extends `Iterable<E>` and defines the core operations common across List/Set/Queue: `add()`, `remove()`, `size()`, `contains()`, `isEmpty()`, `clear()`, `iterator()`, and bulk operations (`addAll()`, `removeAll()`, `retainAll()`, `containsAll()`).

### Q7. What is the difference between `List`, `Set`, and `Queue` at the interface level?
- **`List<E>`** — an **ordered** sequence, allows **duplicates**, elements accessible by **integer index** (`get(int)`, `set(int, E)`).
- **`Set<E>`** — models mathematical set semantics: **no duplicate** elements (as determined by `equals()`), generally **no index-based access**.
- **`Queue<E>`** — designed for holding elements before processing, typically following FIFO order (though `PriorityQueue` and `Deque` offer other orderings), with specialized `offer()`/`poll()`/`peek()` methods designed to signal failure via return value rather than exception.

### Q8. What is `SortedSet` and `NavigableSet`, and how do they extend `Set`?
`SortedSet<E>` extends `Set` to maintain elements in **sorted order** (natural ordering or via a supplied `Comparator`), adding methods like `first()`, `last()`, `headSet()`, `tailSet()`. `NavigableSet<E>` further extends `SortedSet` with additional navigation methods: `lower()`, `floor()`, `ceiling()`, `higher()` (finding the closest element relative to a given value), and supports **descending iteration** (`descendingSet()`, `descendingIterator()`). `TreeSet` is the primary implementation of `NavigableSet`.

### Q9. What is `SortedMap` and `NavigableMap`, and how do they relate to `SortedSet`/`NavigableSet`?
Analogous concepts applied to `Map`: `SortedMap<K, V>` maintains keys in sorted order, providing `firstKey()`, `lastKey()`, `headMap()`, `tailMap()`. `NavigableMap<K, V>` extends this with `lowerKey()`, `floorKey()`, `ceilingKey()`, `higherKey()`, and descending views — `TreeMap` is the primary implementation.

### Q10. What is the difference between `Collection` and `Collections`?
`Collection` (singular) is an **interface** — the root type representing a group of elements. `Collections` (plural) is a **utility class** (`java.util.Collections`) containing static helper methods that operate on collections — sorting, searching, synchronizing, creating immutable/empty views, etc. (analogous to how `Arrays` is a utility class for working with arrays). The naming similarity is a common source of confusion for beginners.

### Q11. Why doesn't `Map` extend `Collection`?
Because `Map`'s core abstraction — key-value **pairs**, with keys required to be unique — doesn't cleanly fit `Collection`'s single-element-based contract (`add(E)`, `contains(E)`, etc.). Rather than forcing an awkward fit, the Collections Framework designers made `Map` a **separate** top-level interface, while still providing collection-based **views** into a map's contents (`keySet()` returns a `Set<K>`, `values()` returns a `Collection<V>`, `entrySet()` returns a `Set<Map.Entry<K,V>>`) so map contents can still be iterated/processed using standard collection idioms.

### Q12. What is `Map.Entry`, and how is it used?
A nested interface within `Map` representing a single key-value pair within the map, returned when iterating over `map.entrySet()`. Provides `getKey()`, `getValue()`, and `setValue(V)` (which updates the underlying map, when supported), commonly used for efficiently iterating over both keys and values together without needing separate key lookups.
```java
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " = " + entry.getValue());
}
```

---

## 3. List Implementations

### Q13. What is `ArrayList`, and how is it internally implemented?
`ArrayList<E>` is backed by a **dynamically resizable array**. When the internal array reaches capacity and a new element is added, a new, larger array (typically 1.5x the current capacity in most JDK implementations) is allocated, and existing elements are copied over — this resizing operation is relatively expensive (O(n)) but happens infrequently (amortized O(1) for `add()` at the end).

### Q14. What is `LinkedList`, and how does it differ from `ArrayList` internally?
`LinkedList<E>` is implemented as a **doubly linked list** — each element is stored in a node containing references to both the previous and next nodes, rather than in a contiguous array. It implements both `List` and `Deque`, making it usable as a general-purpose list, a stack, or a queue.

### Q15. What are the performance trade-offs between `ArrayList` and `LinkedList`?
| Operation | `ArrayList` | `LinkedList` |
|---|---|---|
| Random access (`get(i)`) | O(1) | O(n) — must traverse from an end |
| Add/remove at the **end** | O(1) amortized | O(1) |
| Add/remove at the **beginning** | O(n) — must shift all elements | O(1) |
| Add/remove in the **middle** | O(n) — shifting | O(n) to *find* the position, O(1) to actually insert once found |
| Memory overhead | Lower (contiguous array, some unused capacity) | Higher (each node stores two extra object references) |

In practice, `ArrayList` is the default/preferred choice for most use cases due to better cache locality (contiguous memory) and lower per-element overhead — `LinkedList` is chosen specifically when frequent insertions/removals at the beginning/middle (via an iterator, not by index) are the dominant access pattern, though even then, `ArrayDeque` often outperforms `LinkedList` for queue/stack use cases.

### Q16. Why is inserting into the middle of a `LinkedList` still effectively O(n) overall, despite the actual insertion itself being O(1)?
Because reaching the insertion point in the first place requires **sequentially traversing** the list from the nearest end (there's no random-access capability, unlike an array) — the traversal to *find* the position dominates the cost, making the practical overall complexity of a middle insertion by index O(n), even though the pointer-relinking operation itself, once positioned, is O(1).

### Q17. What is `Vector`, and how does it differ from `ArrayList`?
`Vector<E>` is a legacy (pre-Java 1.2) dynamic array implementation, functionally very similar to `ArrayList`, but with all its methods **synchronized** (thread-safe, but with performance overhead even in single-threaded contexts where that safety is unneeded). `Vector` is now considered largely obsolete — `ArrayList` (optionally wrapped with `Collections.synchronizedList()` or replaced with a proper concurrent collection when thread safety is genuinely needed) is the modern preferred choice.

### Q18. What is `Stack`, and why is it generally discouraged in modern code?
`Stack<E>` is a legacy class extending `Vector`, providing classic LIFO stack operations (`push()`, `pop()`, `peek()`). It's discouraged because it inherits `Vector`'s synchronization overhead and, more fundamentally, because extending `Vector` means `Stack` also exposes `Vector`'s full `List` interface (allowing arbitrary index-based insertion/access, breaking pure LIFO discipline) — `ArrayDeque` is the modern recommended replacement for stack-like use cases.

### Q19. How do you choose an initial capacity for an `ArrayList`, and why might it matter?
```java
List<String> list = new ArrayList<>(1000); // pre-sized capacity, not size
```
If the approximate number of elements is known in advance, specifying an initial capacity avoids the overhead of multiple internal resize/copy operations as the list grows organically from a smaller default capacity — a minor but sometimes meaningful performance optimization for large lists built in a tight loop. Note this sets **capacity**, not actual size — `list.size()` remains 0 until elements are actually added.

### Q20. What is `CopyOnWriteArrayList`, and when is it used?
A thread-safe `List` implementation (from `java.util.concurrent`) that creates a **new copy of the entire underlying array** on every mutating operation (`add`, `remove`, `set`), while reads never require locking and always operate against a stable, unchanging snapshot array. This makes it ideal for scenarios with **many more reads than writes** (e.g., a list of event listeners rarely modified but frequently iterated), since writes are expensive (O(n) copy) but reads are extremely fast and never blocked/interfered with — poor choice for write-heavy use cases.

---

## 4. Set Implementations

### Q21. What is `HashSet`, and how does it guarantee uniqueness?
`HashSet<E>` is backed internally by a `HashMap<E, Object>` (elements are stored as keys, with a single shared dummy constant value) — uniqueness is enforced by `HashMap`'s own key-uniqueness guarantee, relying on the elements' `hashCode()` and `equals()` implementations to detect and reject duplicates. It provides **no ordering guarantee** — iteration order is unspecified and can even change over time (e.g., after resizing).

### Q22. What is `LinkedHashSet`, and how does it differ from `HashSet`?
`LinkedHashSet<E>` extends `HashSet` but additionally maintains a **doubly linked list** running through all its entries, preserving **insertion order** during iteration — giving predictable iteration order (unlike plain `HashSet`) while still providing the same O(1) average-case performance for `add`/`remove`/`contains`, at the cost of slightly higher memory overhead for maintaining the linked list structure.

### Q23. What is `TreeSet`, and how does it maintain order?
`TreeSet<E>` is backed by a `TreeMap` internally (a **red-black tree**, a self-balancing binary search tree), maintaining elements in **sorted order** at all times (either natural ordering via `Comparable`, or a custom order via a supplied `Comparator`). Operations (`add`, `remove`, `contains`) run in **O(log n)** — slower than `HashSet`'s average O(1), but `TreeSet` provides ordered iteration and range/navigation operations (`first()`, `last()`, `higher()`, `lower()`, `subSet()`) that `HashSet` cannot offer at all.

### Q24. How do you choose between `HashSet`, `LinkedHashSet`, and `TreeSet`?
- **`HashSet`** — default choice when you just need uniqueness and don't care about iteration order; fastest average-case performance.
- **`LinkedHashSet`** — when uniqueness **and** predictable (insertion-order) iteration are both needed.
- **`TreeSet`** — when elements must be maintained in **sorted** order, or when range-based navigation operations are needed; accept the O(log n) performance trade-off for this ordering guarantee.

### Q25. What is `EnumSet`, and why is it more efficient than a general-purpose `Set` for enum values?
A specialized `Set` implementation designed exclusively for use with **enum types**, internally represented as a compact **bit vector** (each enum constant corresponds to a single bit) — making operations extremely fast and memory-efficient compared to a general-purpose hash-based or tree-based set, since enum constants have a small, fixed, known universe of possible values that map naturally onto bit positions.

### Q26. Can a `Set` contain `null`? Does this differ across implementations?
`HashSet` and `LinkedHashSet` permit a **single** `null` element. `TreeSet` generally does **not** permit `null` (attempting to add `null` throws a `NullPointerException` in most cases), since comparing `null` against other elements via `compareTo()`/a `Comparator` is generally undefined/problematic — an important distinction to be aware of when choosing an implementation for data that might include nulls.

---

## 5. Map Implementations

### Q27. What is `HashMap`, and how does it work internally (at a high level)?
`HashMap<K, V>` stores key-value pairs in an internal **array of buckets**. A key's `hashCode()` is used (after an internal "hash spreading" step) to determine which bucket it belongs to; within a bucket, entries are stored as a **linked list** (or, since Java 8, a **balanced tree** if a bucket becomes sufficiently large — see Q40) to handle **hash collisions** (multiple keys mapping to the same bucket). Lookups (`get`), insertions (`put`), and removals (`remove`) run in **average O(1)** time, assuming a reasonably well-distributed hash function.

### Q28. What is a hash collision, and how does `HashMap` handle it?
A collision occurs when two distinct keys produce the **same bucket index** (either due to identical hash codes, or different hash codes that map to the same bucket after modulo/masking against the current table size). `HashMap` handles collisions via **separate chaining** — each bucket holds a linked list (or tree, for large buckets) of all entries that hash to that bucket, and `get()`/`put()` traverse that structure using `equals()` to find the exact matching key within the bucket.

### Q29. What is the load factor in `HashMap`, and what is its default value?
The load factor determines the threshold ratio of (number of entries) / (current bucket array capacity) at which the internal table is **resized** (typically doubled) — the default is **0.75**. A lower load factor trades increased memory usage (more, sparser buckets) for reduced collision likelihood/faster lookups; a higher load factor is more memory-efficient but increases collision frequency and degrades average lookup performance.

### Q30. What happens during `HashMap` resizing (rehashing)?
When the number of entries exceeds `capacity * loadFactor`, the internal bucket array is **resized** (typically doubled in size), and **every existing entry** must be **rehashed and redistributed** into the new, larger bucket array (since the bucket index calculation depends on the table's size) — an expensive O(n) operation, though it happens infrequently enough that `put()` remains amortized O(1) on average.

### Q31. What is `LinkedHashMap`, and how does it differ from `HashMap`?
Like `LinkedHashSet` relative to `HashSet`, `LinkedHashMap<K, V>` extends `HashMap` while additionally maintaining a doubly linked list preserving either **insertion order** (default) or, optionally, **access order** (configurable via a constructor flag) — access-order mode is particularly useful for implementing an **LRU (Least Recently Used) cache**, since it can be configured to move recently-accessed entries to the end of iteration order automatically.

### Q32. How would you implement a simple LRU cache using `LinkedHashMap`?
```java
class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int capacity;
    LRUCache(int capacity) {
        super(16, 0.75f, true); // true = access-order
        this.capacity = capacity;
    }
    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > capacity;
    }
}
```
Overriding `removeEldestEntry()` (a protected hook method specifically designed for this purpose) to return `true` once the map exceeds a target capacity automatically evicts the least-recently-used entry on each subsequent `put()`, combined with access-order mode to keep recently-used entries from being evicted.

### Q33. What is `TreeMap`, and what guarantees does it provide?
`TreeMap<K, V>` is implemented as a **red-black tree**, maintaining keys in **sorted order** at all times (natural ordering or a custom `Comparator`). Guarantees O(log n) for `get`/`put`/`remove`, supports range views (`headMap()`, `tailMap()`, `subMap()`) and navigation methods (`firstKey()`, `lastKey()`, `higherKey()`, `lowerKey()`) — the map-equivalent of `TreeSet`, and indeed `TreeSet` is built on top of `TreeMap` internally.

### Q34. What is `Hashtable`, and how does it differ from `HashMap`?
`Hashtable<K, V>` is a **legacy** (pre-Java 1.2) class, functionally similar to `HashMap` but with all methods **synchronized** (thread-safe but with unconditional locking overhead), and — notably — it does **not permit `null` keys or `null` values** at all (unlike `HashMap`, which permits one `null` key and multiple `null` values). Considered obsolete in modern code; `ConcurrentHashMap` is the preferred thread-safe alternative when concurrency is genuinely needed.

### Q35. Can `HashMap` contain `null` keys/values, and are there any restrictions?
`HashMap` permits **at most one `null` key** (stored, by convention, in bucket index 0) and **any number of `null` values**. `Hashtable` and `ConcurrentHashMap` do **not** permit null keys or null values at all (attempting to insert either throws a `NullPointerException`) — `ConcurrentHashMap`'s prohibition specifically exists because, in a concurrent context, a `null` return from `get()` would be ambiguous (unclear whether it means "key not present" or "key present with a null value"), which is problematic for atomic check-then-act operations in a multithreaded setting.

### Q36. What is `IdentityHashMap`, and how does it differ from `HashMap`?
A specialized `Map` implementation that uses **reference equality** (`==`) instead of `equals()`/`hashCode()` for comparing keys (and, correspondingly, uses `System.identityHashCode()` rather than the key's own `hashCode()`) — useful in niche scenarios like object graph traversal/serialization frameworks, where you specifically need to distinguish between distinct objects that might otherwise be considered "equal" by their own `equals()` implementation.

### Q37. What is `WeakHashMap`, and what is it used for?
A `Map` implementation where **keys** are held via **weak references** — allowing the garbage collector to reclaim a key (and automatically remove its corresponding entry from the map) once that key is no longer strongly referenced anywhere else in the application, even while it remains a key in this map. Useful for building caches or metadata associations that shouldn't themselves prevent garbage collection of the associated keys — conceptually analogous to JavaScript's `WeakMap`.

### Q38. What is `EnumMap`, and why is it more efficient than a general-purpose `Map` for enum keys?
Similar to `EnumSet`, `EnumMap<K extends Enum<K>, V>` is a specialized, highly efficient `Map` implementation for enum-typed keys, internally backed by a simple **array** indexed by each enum constant's ordinal position — avoiding hashing entirely and providing better performance and lower memory overhead than a general-purpose `HashMap` for this specific use case, while also naturally iterating in the enum constants' declared (natural) order.

---

## 6. Queue & Deque Implementations

### Q39. What is the difference between `Queue` and `Deque`?
`Queue<E>` models a typically **FIFO** (first-in-first-out) collection, with operations for adding to the tail and removing from the head. `Deque<E>` ("double-ended queue") extends the concept to allow efficient insertion/removal at **both ends**, making it usable as either a FIFO queue or a **LIFO stack** — `Deque` is generally considered the modern, more flexible/efficient replacement for both the legacy `Stack` class and `LinkedList`-as-a-queue usage.

### Q40. What is the difference between the "throws exception" and "returns special value" method pairs in `Queue` (`add`/`offer`, `remove`/`poll`, `element`/`peek`)?
`Queue` provides two parallel sets of methods for the same operations, differing in failure behavior: `add()`/`remove()`/`element()` **throw an exception** on failure (e.g., `remove()` on an empty queue throws `NoSuchElementException`), while `offer()`/`poll()`/`peek()` **return a special value** (`false` or `null`) instead of throwing — `offer`/`poll`/`peek` are generally preferred for typical queue-processing code where an empty/full queue is an expected, routine condition rather than an exceptional one.

### Q41. What is `ArrayDeque`, and why is it generally preferred over `LinkedList` and `Stack` for queue/stack use cases?
`ArrayDeque<E>` is a **resizable array-based** implementation of `Deque`, providing O(1) amortized performance for adding/removing at both ends. It's generally faster and more memory-efficient than `LinkedList` for queue/deque/stack use cases (better cache locality from contiguous array storage, no per-element node/pointer overhead), and — unlike `Stack`/`Vector` — it's **not synchronized**, avoiding unnecessary locking overhead in single-threaded contexts. The official Java documentation itself recommends `ArrayDeque` over `Stack`/`LinkedList` for typical stack/queue needs.

### Q42. What is `PriorityQueue`, and how does it determine element ordering?
A `Queue` implementation backed by a **binary heap** (specifically, a min-heap by default), where elements are ordered according to their **natural ordering** (via `Comparable`) or a supplied `Comparator` — `poll()`/`peek()` always return the **smallest** (highest-priority) element according to that ordering, not necessarily in insertion order. Insertion (`offer`) and removal (`poll`) both run in **O(log n)**, while `peek()` (viewing the head without removing) is O(1).

### Q43. Does `PriorityQueue`'s iterator return elements in priority order?
**No** — iterating over a `PriorityQueue` via its `iterator()` does **not** guarantee any particular order (specifically, not sorted/priority order); it only reflects the underlying heap array's internal storage order. Only repeatedly calling `poll()` guarantees elements are retrieved in priority order, one at a time.

### Q44. What is `BlockingQueue`, and what problem does it solve?
An interface (`java.util.concurrent`) extending `Queue`, adding operations that **block** the calling thread when the queue is full (on insertion) or empty (on retrieval) until space/an element becomes available — a fundamental building block for **producer-consumer** concurrency patterns, where one or more threads produce items to a shared queue while other threads consume them, without needing manual wait/notify synchronization logic. Common implementations: `ArrayBlockingQueue` (bounded, array-backed), `LinkedBlockingQueue` (optionally bounded, linked-node-backed), `PriorityBlockingQueue`, `SynchronousQueue` (a queue with **zero** capacity — every insertion must be matched by a corresponding, simultaneous removal).

---

## 7. Iterators & Iteration

### Q45. What is an `Iterator`, and what methods does it define?
An object that provides sequential access to a collection's elements, defined by `hasNext()` (checks if more elements remain), `next()` (returns the next element, advancing the cursor), and `remove()` (an optional operation — removes the last element returned by `next()` from the underlying collection). It's the mechanism underlying the enhanced for-loop (`for (E e : collection)`) under the hood.

### Q46. What is a `ListIterator`, and how does it extend `Iterator`?
A specialized iterator available only for `List` implementations, extending `Iterator` with **bidirectional** traversal (`hasPrevious()`, `previous()`), the ability to obtain the current cursor position (`nextIndex()`, `previousIndex()`), and, notably, the ability to **modify** the list during iteration via `set(E)` (replacing the last returned element) and `add(E)` (inserting a new element at the current position) — operations not available on a plain `Iterator`.

### Q47. What is a `ConcurrentModificationException`, and when does it occur?
Thrown when a collection is **structurally modified** (elements added/removed, not merely having their values changed) while it's being iterated over via an iterator that wasn't itself responsible for that modification — detected via a **fail-fast** mechanism (see Q48). Common trigger: calling `list.remove(element)` directly (rather than `iterator.remove()`) from within an enhanced for-loop over that same list.
```java
for (String s : list) {
    if (s.equals("target")) list.remove(s); // throws ConcurrentModificationException
}
```

### Q48. What does "fail-fast" mean in the context of Java iterators, and how is it implemented?
Fail-fast iterators (used by most standard non-concurrent collections like `ArrayList`, `HashMap`, `HashSet`) detect structural modifications made outside the iterator itself and **immediately throw** `ConcurrentModificationException` on the **next** call to `next()`/similar, rather than allowing the iteration to silently produce inconsistent or unpredictable results. Implemented internally via a **modification count** (`modCount`) field on the collection, incremented on every structural change — the iterator captures the `modCount` value at creation and compares it on each subsequent operation, throwing if it detects a mismatch. Note: this is a **best-effort** detection mechanism, not a hard guarantee — it's explicitly documented as not something to rely on for correctness in genuinely concurrent scenarios.

### Q49. What is the correct way to remove elements from a collection while iterating over it?
Use the iterator's own `remove()` method, which is specifically designed to allow safe modification during iteration without triggering `ConcurrentModificationException` (since the iterator itself tracks/updates the collection's internal state consistently as part of this call):
```java
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    if (it.next().equals("target")) it.remove(); // safe
}
```
Alternatively, `Collection.removeIf(Predicate)` (Java 8+) provides a concise, safe way to remove matching elements without manually managing an iterator at all.

### Q50. What is "fail-safe" iteration, and which collections provide it?
Fail-safe iterators operate on a **separate copy/snapshot** of the collection's data (or otherwise tolerate concurrent modification gracefully) rather than throwing an exception when the underlying collection changes during iteration — provided by concurrent collections like `CopyOnWriteArrayList` and `ConcurrentHashMap`. The trade-off: the iteration may not reflect the very latest modifications made concurrently during that iteration (it might see a "stale" snapshot from roughly when iteration began), but it avoids the abrupt exception-throwing behavior of fail-fast iterators — an appropriate trade-off specifically for genuinely concurrent access scenarios.

### Q51. What is the difference between the enhanced for-loop (`for-each`) and an explicit `Iterator`?
The enhanced for-loop is **syntactic sugar** compiled down to use an `Iterator` internally (for any `Iterable`) — functionally equivalent for simple traversal, but it doesn't give you direct access to the iterator itself, meaning you **cannot** call `iterator.remove()` (or other iterator-specific operations) from within a for-each loop — an explicit `Iterator`/`ListIterator` reference is required for those use cases.

---

## 8. Comparable & Comparator

### Q52. What is the difference between `Comparable` and `Comparator`?
`Comparable<T>` is implemented **by the class itself** being compared, defining a single `compareTo(T other)` method that establishes the class's **natural ordering** (a class can only have one natural ordering). `Comparator<T>` is a **separate**, external object defining a `compare(T a, T b)` method, allowing **multiple different orderings** to be defined for the same type without modifying the type itself — useful when sorting by different criteria in different contexts, or when you can't modify the class's source code (e.g., a third-party library class).

### Q53. What does `compareTo()`/`compare()` return, and what do the return values mean?
Both return an `int`: a **negative** value if the first argument is "less than" the second, **zero** if they're considered equal (for ordering purposes), and a **positive** value if the first is "greater than" the second — the exact magnitude is unimportant (only the sign matters for typical sorting purposes), though by convention, magnitude sometimes reflects the "distance" between values.

### Q54. How do you implement `Comparable` for a custom class?
```java
class Person implements Comparable<Person> {
    String name;
    int age;
    @Override
    public int compareTo(Person other) {
        return Integer.compare(this.age, other.age); // ascending by age
    }
}
```
`Integer.compare()` (and similar static `compare()` methods on wrapper classes) is preferred over manual subtraction (`this.age - other.age`), since subtraction can silently **overflow** for values near `Integer.MIN_VALUE`/`MAX_VALUE`, producing an incorrect sign.

### Q55. How do you create a `Comparator` using the modern, chainable Java 8+ style?
```java
Comparator<Person> byAge = Comparator.comparingInt(p -> p.age);
Comparator<Person> byNameThenAge = Comparator.comparing((Person p) -> p.name)
    .thenComparingInt(p -> p.age);
Comparator<Person> byAgeDescending = byAge.reversed();
```
`Comparator.comparing()`/`comparingInt()`/`comparingLong()`/`comparingDouble()` (avoiding unnecessary autoboxing for primitives), `.thenComparing()` for tie-breaking on a secondary criterion, and `.reversed()` — together provide a concise, fluent way to build complex comparators without writing verbose anonymous classes.

### Q56. How do you sort a `List` using `Comparable` vs. a custom `Comparator`?
```java
Collections.sort(people);                       // uses Person's natural ordering (Comparable)
people.sort(Comparator.comparing(p -> p.name));  // uses an explicit Comparator
Collections.sort(people, byAgeDescending);       // equivalent older-style call
```
`List.sort(Comparator)` (Java 8+) is the modern preferred instance method; passing `null` to `list.sort(null)` sorts by natural ordering, equivalent to relying on `Comparable`.

### Q57. What happens if you call `Collections.sort()` on a list of objects that don't implement `Comparable`, without supplying a `Comparator`?
It fails to **compile** if using generics correctly (`Collections.sort(List<T> list)` requires `T` to extend `Comparable<? super T>`) — the compiler enforces this constraint. If somehow bypassed (e.g., via raw types), it would throw a `ClassCastException` at runtime when the sort algorithm attempts to invoke a non-existent `compareTo()` method.

### Q58. What is the general contract a well-behaved `compareTo()`/`compare()` implementation must satisfy?
It should be **consistent** (sign of `compare(a, b)` is the reverse of `compare(b, a)`), **transitive** (if `a > b` and `b > c`, then `a > c`), and ideally **consistent with `equals()`** (though not strictly required by the interface contract — `compareTo(a, b) == 0` should generally, but doesn't absolutely have to, imply `a.equals(b)`; violating this can cause surprising behavior in sorted collections like `TreeSet`/`TreeMap`, which use `compareTo`/`compare`, **not** `equals()`, to determine element/key equality).

---

## 9. `equals()`, `hashCode()` & Object Contracts

### Q59. What is the general contract between `equals()` and `hashCode()`, and why must they be overridden together?
The contract (defined by `Object`'s documentation) states: if two objects are equal according to `equals()`, they **must** have the same `hashCode()`. The reverse isn't required (unequal objects *can* share a hash code — a collision, which is normal and expected). If you override `equals()` **without** correspondingly overriding `hashCode()` (or vice versa), you violate this contract, causing broken behavior in hash-based collections (`HashMap`, `HashSet`) — e.g., two "equal" objects might end up in different buckets, causing lookups to fail unexpectedly, since `HashMap`/`HashSet` use `hashCode()` to locate the correct bucket **before** using `equals()` to confirm an exact match within it.

### Q60. What happens if you use a mutable object as a `HashMap` key or `HashSet` element, and its fields change after insertion?
If a field used in that object's `hashCode()` computation changes after the object has been inserted, its hash code changes — but the object remains stored in its **original** bucket (based on its hash code *at insertion time*). Subsequent lookups using the *now-different* hash code will search the *wrong* bucket, **failing to find the object** even though it's technically still present in the map/set — a classic bug from using mutable keys, which is why immutable objects (or, at minimum, objects whose relevant fields don't change post-insertion) are strongly recommended as hash-based collection keys.

### Q61. What are the general requirements/properties of a well-behaved `equals()` implementation?
Per `Object`'s contract, `equals()` must be: **reflexive** (`x.equals(x)` is always true), **symmetric** (`x.equals(y)` iff `y.equals(x)`), **transitive** (if `x.equals(y)` and `y.equals(z)`, then `x.equals(z)`), **consistent** (repeated calls return the same result, assuming no relevant fields change), and `x.equals(null)` must always return `false`.

### Q62. What is the typical structure of a correctly implemented `equals()` method?
```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    Person person = (Person) o;
    return age == person.age && Objects.equals(name, person.name);
}

@Override
public int hashCode() {
    return Objects.hash(name, age);
}
```
`Objects.equals()` (null-safe field comparison) and `Objects.hash()` (a convenient, well-distributed multi-field hash combination) are the modern, recommended utilities for implementing both methods correctly and concisely (or, more commonly today, generated automatically by the IDE, a code generator like Lombok, or — for immutable data carriers — using a `record`, which auto-generates both correctly).

### Q63. How does `==` differ from `equals()` for objects, and what about for `String`s specifically?
`==` always compares **reference identity** (are these two variables pointing to the exact same object in memory) for reference types, regardless of any `equals()` override. `equals()` (when properly overridden) compares logical/structural **equality** as defined by the class. For `String`, this distinction is a very common source of bugs: `new String("a") == new String("a")` is `false` (different object instances), while `.equals()` correctly returns `true`; additionally, **string literals** are interned/cached by the JVM (the "string pool"), so `"a" == "a"` often (though not something to rely on) returns `true` for literals specifically, adding to common confusion — `.equals()` should always be used for string content comparison, never `==`.

### Q64. Why does a `record` (Java 16+) simplify implementing value-like classes for use in collections?
A `record` automatically generates a correct, field-based `equals()`, `hashCode()`, and `toString()` implementation (based on all its declared components) at compile time — eliminating an entire class of common bugs from manually (or even IDE-generated, but potentially forgotten/inconsistent) implementations, and making it a natural, low-boilerplate fit for simple, immutable data objects intended for use as map keys or set elements.

---

## 10. Generics in Collections

### Q65. What are generics, and why are they important for the Collections Framework?
Generics (introduced in Java 5) allow classes/interfaces/methods to be parameterized by **type**, enabling collections to be declared with a specific element type (`List<String>` instead of a raw `List`) — providing **compile-time type safety** (catching type mismatches before runtime) and eliminating the need for manual casting when retrieving elements, which was required in pre-generics Java code operating on raw, `Object`-typed collections.

### Q66. What is type erasure, and what does it mean for generics at runtime?
Java implements generics via **type erasure** — generic type information (`<String>`, `<Integer>`) exists only at **compile time** for type-checking purposes, and is essentially "erased" (replaced with `Object`, or the type's upper bound) in the compiled bytecode. This is why you cannot, for example, check `if (list instanceof List<String>)` at runtime (only `list instanceof List` is legal — the generic parameter type is unavailable at runtime), and why `List<String>.class` and `List<Integer>.class` are actually the exact same `Class` object at runtime.

### Q67. What is the difference between `List<Object>` and a raw `List`?
`List<Object>` is a properly parameterized generic type, explicitly stating it can hold any `Object` — fully type-checked, just permissively so. A **raw type** `List` (used without any type parameter at all) opts entirely out of generic type checking, essentially reverting to pre-Java-5 behavior (allowing any type to be added, with casting required on retrieval, and no compile-time safety) — raw types are strongly discouraged in modern code (mostly retained in the language purely for backward compatibility with legacy code) and typically trigger an "unchecked" compiler warning.

### Q68. What are bounded type parameters, and what does `<T extends Comparable<T>>` mean?
Bounded type parameters restrict what types can be substituted for a generic parameter. `<T extends Comparable<T>>` (commonly seen in generic sorting/searching method signatures) requires that `T` must implement `Comparable<T>` — ensuring the method can safely call `.compareTo()` on instances of `T`, which wouldn't be guaranteed/available for a fully unbounded `<T>`.

### Q69. What is the difference between `List<? extends Number>` and `List<? super Number>` (wildcards), and what is the PECS mnemonic?
- **`List<? extends Number>`** (upper-bounded wildcard) — represents a list of **some unknown subtype** of `Number`; you can safely **read** `Number`s from it (any element is guaranteed to be at least a `Number`), but you **cannot add** to it (aside from `null`), since the compiler doesn't know the list's *exact* runtime type (it could be a `List<Integer>`, and adding a `Double` would violate that).
- **`List<? super Number>`** (lower-bounded wildcard) — represents a list of `Number` or any of its **supertypes**; you can safely **add** `Number`s (or subtypes) to it, but reading from it only guarantees you get back an `Object` (since the actual list could be a `List<Object>`).

**PECS** ("Producer Extends, Consumer Super") is the standard mnemonic: use `extends` when the collection is a data **source** you're only reading from ("producing" data to you), and `super` when the collection is a data **destination** you're only writing to ("consuming" data from you).

### Q70. Why can't you create a generic array in Java (e.g., `new T[10]`), and how does this relate to collections?
Due to **type erasure**, the JVM has no runtime knowledge of `T`'s actual type at the point of array creation, and arrays (unlike generic collections) retain their **component type** at runtime (enabling runtime array-store-type-checking) — allowing `new T[10]` would undermine that runtime guarantee, so the language disallows it entirely. This is one practical reason the Collections Framework (backed by generic, type-erased structures rather than raw arrays of a generic type) is generally preferred over hand-rolled generic array-based structures in application code.

---

## 11. Collections Utility Class & Immutability

### Q71. What are some commonly used static methods on the `Collections` utility class?
`Collections.sort(list)`, `Collections.reverse(list)`, `Collections.shuffle(list)`, `Collections.max(collection)`/`min(collection)`, `Collections.frequency(collection, element)` (counts occurrences), `Collections.unmodifiableList/Set/Map(collection)` (returns a read-only view), `Collections.synchronizedList/Set/Map(collection)` (returns a synchronized wrapper), `Collections.emptyList()`/`singletonList(element)`, and `Collections.binarySearch(list, key)` (requires the list to already be sorted).

### Q72. What is the difference between `Collections.unmodifiableList()` and the `List.of()` factory method (Java 9+)?
`Collections.unmodifiableList(list)` returns a **view** wrapping an existing, mutable list — attempts to modify it directly through the wrapper throw `UnsupportedOperationException`, but the underlying original list can **still be mutated directly**, and those changes **will be reflected** through the "unmodifiable" view (since it's just a thin wrapper, not an independent copy) — a common point of confusion. `List.of(...)` (and `Set.of()`, `Map.of()`) creates a genuinely **immutable**, independent collection from scratch, with no underlying mutable backing collection that could be changed elsewhere — a cleaner, safer guarantee.

### Q73. What are the key differences between `List.of()` (Java 9+ immutable factory methods) and `Arrays.asList()`?
`Arrays.asList(array)` returns a **fixed-size** (but not fully immutable) list backed directly by the given array — you **can** call `set()` to modify existing elements (which also modifies the underlying array), but **cannot** `add()`/`remove()` (throws `UnsupportedOperationException`, since that would require resizing the fixed-size backing array). `List.of(...)` creates a **truly immutable** list — `set()`, `add()`, and `remove()` **all** throw `UnsupportedOperationException`. Additionally, `List.of()` does not permit `null` elements at all, while `Arrays.asList()` does.

### Q74. Why are immutable collections generally recommended, and what are their benefits?
- **Thread safety** — an immutable collection can be freely shared across threads without any synchronization, since it can never be concurrently modified.
- **Predictability/safety** — eliminates an entire class of bugs from unexpected external mutation of a collection you didn't intend to be modified.
- **Suitable as `HashMap`/`HashSet` keys/elements** — avoids the mutable-key hazard described in Q60.
- **API design clarity** — returning an immutable collection from a method signals to callers that they shouldn't attempt to modify the result.

### Q75. What is `Collections.synchronizedList()`/`synchronizedMap()`, and what are its limitations compared to the dedicated `java.util.concurrent` collections?
Wraps an existing collection with a synchronized wrapper, where each **individual** method call is internally synchronized (using a single shared lock) — providing basic thread safety for individual operations, but **not** for compound/multi-step operations (e.g., "check-then-act" sequences like "if the list doesn't contain X, add it" still require **external, manual synchronization** around the whole sequence to be truly atomic). Additionally, **iterating** over a synchronized wrapper still requires manual external synchronization around the entire iteration (the wrapper's own per-call locking doesn't protect against concurrent modification during your iteration loop) — the dedicated concurrent collections (`ConcurrentHashMap`, `CopyOnWriteArrayList`) generally offer better performance and more genuinely useful concurrent semantics, and are preferred in modern code over the older `synchronizedXxx()` wrapper approach.

---

## 12. Streams & Functional Operations on Collections

### Q76. How do the Streams API (Java 8+) and the Collections Framework relate to each other?
`Collection` extends `Iterable`, which (since Java 8) provides a default `stream()` method — allowing any collection to be converted into a **Stream**, enabling a fluent, functional-style pipeline of operations (`filter`, `map`, `sorted`, `collect`, `reduce`, etc.) as an alternative (not a replacement) to traditional imperative iteration — streams themselves are not a collection/data structure, but rather a sequence of computational steps to be applied.

### Q77. How do you convert a `List` to a `Stream` and back?
```java
List<String> upperNames = names.stream()
    .filter(n -> n.length() > 3)
    .map(String::toUpperCase)
    .collect(Collectors.toList()); // or .toList() (Java 16+, returns an unmodifiable list)
```

### Q78. What are common `Collectors` used to gather stream results back into collections?
`Collectors.toList()`, `.toSet()`, `.toMap(keyFn, valueFn)`, `.joining(delimiter)` (for strings), `.groupingBy(classifierFn)` (produces a `Map<K, List<T>>` grouping elements by a computed key), `.partitioningBy(predicate)` (produces a `Map<Boolean, List<T>>` splitting elements into two groups), and `.counting()`/`.summingInt()`/`.averagingDouble()` (often combined with `groupingBy` as a downstream collector for aggregation).
```java
Map<String, List<Person>> byCity = people.stream()
    .collect(Collectors.groupingBy(p -> p.city));
```

### Q79. What is the difference between `map()` and `flatMap()` in the Streams API?
`map()` transforms each element into exactly **one** new element (a 1-to-1 transformation). `flatMap()` transforms each element into a **stream of elements**, then flattens all those resulting streams into a single combined stream — used when each input element maps to zero, one, or many output elements (e.g., transforming a `List<List<Integer>>` into a single flat `Stream<Integer>`).

### Q80. What is the difference between an intermediate and a terminal stream operation, and what is "laziness"?
**Intermediate operations** (`filter`, `map`, `sorted`) return a new `Stream` and are **lazily evaluated** — they don't actually execute any processing until a **terminal operation** (`collect`, `forEach`, `reduce`, `count`, `anyMatch`) is invoked, at which point the entire pipeline executes in a single pass over the data. This laziness allows the stream to build up an optimized execution plan (and enables working with infinite streams, since intermediate operations like `filter`/`map` don't force full evaluation on their own) — no data is actually processed by intermediate operations alone.

### Q81. Can a `Stream` be reused/iterated more than once?
No — a `Stream` can only be **consumed once**. Attempting to invoke a terminal operation on a stream that's already had a terminal operation performed on it throws an `IllegalStateException` — if you need to process the same data multiple times via streams, you must create a **new** stream from the original source collection each time (`collection.stream()` again).

### Q82. What is a parallel stream, and what are its risks?
`collection.parallelStream()` (or `.stream().parallel()`) attempts to execute the stream's pipeline across **multiple threads** (using the common `ForkJoinPool`) automatically, potentially improving performance for large, CPU-intensive, easily-partitionable workloads. Risks/caveats: overhead from thread coordination can make it **slower** than sequential processing for small datasets or simple operations; operations must be genuinely **stateless and side-effect-free** to produce correct, deterministic results in parallel; and shared mutable state accessed within a parallel stream's operations can introduce subtle race conditions if not handled carefully — parallel streams should be adopted deliberately and measured, not assumed to always be a performance win.

---

## 13. Concurrent Collections

### Q83. What is `ConcurrentHashMap`, and how does it achieve thread safety without a single global lock?
A thread-safe `Map` implementation designed for high-concurrency scenarios, achieving much better throughput than a fully-synchronized `Hashtable`/`Collections.synchronizedMap()` by using **fine-grained locking** — historically (pre-Java 8) locking at the level of individual "segments" of the table, and since Java 8, locking at the level of individual **bins/buckets** (using CAS operations and synchronized blocks scoped to just the specific bucket being modified) — allowing many threads to read and write to **different** parts of the map **concurrently** without contending for a single shared lock.

### Q84. Does `ConcurrentHashMap`'s `size()` method provide a perfectly precise, real-time count under concurrent modification?
Not necessarily a strictly precise instantaneous count in the presence of concurrent modifications happening during the call — `size()` (and similar aggregate methods) provide a **best-effort estimate** that may not reflect an exact, single consistent point-in-time snapshot if the map is being concurrently modified by other threads during the call, a documented and accepted trade-off for avoiding a full-map lock just to compute a count.

### Q85. What atomic compound operations does `ConcurrentHashMap` provide, and why do they matter?
Methods like `putIfAbsent(key, value)`, `computeIfAbsent(key, mappingFunction)`, `compute(key, remappingFunction)`, and `merge(key, value, remappingFunction)` perform their entire check-then-act (or read-modify-write) sequence **atomically**, as a single indivisible operation — critical in concurrent contexts, since separately calling `containsKey()` followed by `put()` (even on a thread-safe map) is **not** atomic as a combined sequence, and a race condition could occur between those two separate calls from different threads.
```java
map.computeIfAbsent(key, k -> new ArrayList<>()).add(value); // atomic get-or-create
```

### Q86. What is `ConcurrentSkipListMap`/`ConcurrentSkipListSet`, and when would you use them over `ConcurrentHashMap`?
Thread-safe, **sorted** collections (based on a **skip list** data structure — a probabilistic, layered linked-list structure providing average O(log n) operations) — used when you need both **thread safety** and **sorted-order** iteration/navigation (analogous to a concurrent version of `TreeMap`/`TreeSet`), which `ConcurrentHashMap`/a concurrent `HashSet` cannot provide, since they offer no ordering guarantee at all.

### Q87. What are the main `java.util.concurrent` collection classes to be familiar with, at a summary level?
- **`ConcurrentHashMap`** — thread-safe, high-throughput hash map.
- **`CopyOnWriteArrayList`/`CopyOnWriteArraySet`** — thread-safe, optimized for read-heavy/infrequent-write use cases.
- **`ConcurrentSkipListMap`/`ConcurrentSkipListSet`** — thread-safe, sorted collections.
- **`BlockingQueue` implementations** (`ArrayBlockingQueue`, `LinkedBlockingQueue`, `PriorityBlockingQueue`, `SynchronousQueue`, `DelayQueue`) — for producer-consumer patterns.
- **`ConcurrentLinkedQueue`/`ConcurrentLinkedDeque`** — non-blocking, lock-free (CAS-based) thread-safe queue/deque implementations for high-throughput, non-blocking use cases.

---

## 14. Internal Implementation Details

### Q88. How does `HashMap` determine which bucket a key belongs to, given its `hashCode()`?
The key's `hashCode()` is passed through an internal **hash-spreading function** (since Java 8, `hash ^ (hash >>> 16)` — XORing the hash with its own upper 16 bits shifted down) to better distribute entries and reduce collisions caused by poor-quality hash functions (particularly ones where only the lower bits vary meaningfully) — the resulting spread hash is then reduced via a bitwise AND against `(capacity - 1)` (equivalent to modulo, since capacity is always a power of two) to determine the actual bucket index.

### Q89. Why does `HashMap`'s internal capacity always need to be a power of two?
Because bucket index calculation uses `hash & (capacity - 1)` as an efficient substitute for `hash % capacity` — this bitwise-AND trick only produces mathematically correct, well-distributed results when `capacity` is a power of two (since `capacity - 1` then produces a contiguous run of all-1 bits, cleanly masking the appropriate low-order bits of the hash) — using the faster bitwise operation instead of an actual modulo operation is a meaningful performance optimization at this frequently-executed, low-level step.

### Q90. What change was introduced in Java 8 regarding `HashMap` buckets with many collisions (treeification)?
Since Java 8, if a single bucket's linked list grows beyond a threshold (default: **8** entries, `TREEIFY_THRESHOLD`) **and** the table's overall capacity is at least 64, that bucket is converted from a linked list into a **balanced red-black tree** — improving worst-case lookup/removal performance within that specific bucket from O(n) (linked list traversal) to O(log n), guarding against pathological performance degradation from a poorly-distributed hash function or, historically, a hash-flooding **denial-of-service attack** targeting hash-based collections with many colliding keys. (Buckets shrink back to a linked list if they later fall below a separate, lower **untreeify** threshold.)

### Q91. How is `ArrayList`'s dynamic resizing (growth) implemented, and what is the typical growth factor?
When `ArrayList`'s internal backing array is full and a new element is added, a new array is allocated at approximately **1.5x** the previous capacity (`oldCapacity + (oldCapacity >> 1)`, in the OpenJDK implementation) — and all existing elements are copied (`Arrays.copyOf` / `System.arraycopy`) into the new array. This growth factor balances memory overhead (not growing too aggressively, wasting memory) against amortized time complexity (not growing too conservatively, which would require excessively frequent, expensive resize operations).

### Q92. What is the internal structure of a `TreeMap`, and why does it guarantee O(log n) operations?
`TreeMap` is implemented as a **red-black tree** — a self-balancing binary search tree that maintains specific coloring/structural invariants (via rotations and recoloring on insertion/deletion) guaranteeing the tree's height never exceeds roughly `2 * log2(n)`, regardless of insertion order — this bounded height is what guarantees O(log n) worst-case performance for search/insert/delete, in contrast to a naive, unbalanced binary search tree, which could degenerate to O(n) performance (effectively becoming a linked list) under adversarial or simply unlucky insertion order.

### Q93. How does `LinkedList` implement the `Deque` interface internally?
Each node in the doubly linked list holds references to both its predecessor and successor nodes, plus the element itself — `Deque` operations at either end (`addFirst`, `addLast`, `removeFirst`, `removeLast`) simply manipulate the head/tail node references and their immediately adjacent links directly, achieving true **O(1)** performance for insertion/removal at either end, since no traversal or shifting is ever required for end-of-list operations specifically (unlike an array-based structure, where inserting at the *front* would require shifting every other element).

---

## 15. Performance & Big-O Complexity

### Q94. What is the time complexity of common operations across the major `List` implementations?
| Operation | `ArrayList` | `LinkedList` |
|---|---|---|
| `get(index)` | O(1) | O(n) |
| `add(element)` (at end) | O(1) amortized | O(1) |
| `add(index, element)` | O(n) | O(n) (to find position) |
| `remove(index)` | O(n) | O(n) (to find position) |
| `contains(element)` | O(n) | O(n) |

### Q95. What is the time complexity of common operations across the major `Set`/`Map` implementations?
| Operation | `HashMap`/`HashSet` | `LinkedHashMap`/`LinkedHashSet` | `TreeMap`/`TreeSet` |
|---|---|---|---|
| `get`/`put`/`contains` | O(1) average, O(log n) worst-case (post-treeification) | O(1) average | O(log n) |
| Iteration order | Unspecified | Insertion (or access) order | Sorted order |
| `first()`/`last()`/range queries | Not supported | Not supported | O(log n) |

### Q96. What is the time complexity of common `Queue`/`Deque` operations?
| Operation | `ArrayDeque` | `LinkedList` | `PriorityQueue` |
|---|---|---|---|
| `offer`/`poll` (at accessible end) | O(1) amortized | O(1) | O(log n) |
| `peek` | O(1) | O(1) | O(1) |
| Arbitrary index access | Not supported | O(n) | Not supported |

### Q97. Why is `HashMap`'s worst-case complexity O(log n) rather than O(n), post-Java 8?
Due to the **treeification** of heavily-collided buckets (Q90) — a bucket with enough entries converts from an O(n)-worst-case linked list into an O(log n)-worst-case balanced tree, bounding the damage a poor hash function (or adversarial input specifically crafted to cause many collisions) can inflict on lookup performance, compared to the purely linked-list-based (worst-case O(n)) behavior in pre-Java-8 `HashMap` implementations.

### Q98. Why does `ArrayList.contains()` remain O(n) even though `get(index)` is O(1)?
Because `contains()` doesn't know the target element's index in advance — it must **linearly scan** the array, comparing each element via `equals()`, until a match is found or the end is reached; `get(index)`'s O(1) performance relies specifically on already knowing the index, which is a fundamentally different operation with a different, unrelated complexity characteristic.

### Q99. How does `TreeSet`/`TreeMap`'s O(log n) performance compare practically to `HashSet`/`HashMap`'s average O(1), and when is the trade-off worth it?
For pure lookup/insertion/removal-heavy workloads with no ordering requirement, `HashSet`/`HashMap`'s average O(1) is typically meaningfully faster in practice than `TreeSet`/`TreeMap`'s O(log n) (especially as the dataset grows large). The trade-off toward the tree-based structures is worthwhile specifically when **sorted iteration order** or **range/navigation queries** (`floor`, `ceiling`, `subMap`, etc.) are genuinely required by the application — a requirement `HashMap`/`HashSet` simply cannot fulfill at all, regardless of their raw speed advantage for simple lookups.

---

## 16. Common Pitfalls & Best Practices

### Q100. Why is it generally recommended to program against interfaces (`List`, `Map`) rather than concrete implementation types (`ArrayList`, `HashMap`) in method signatures/variable declarations?
```java
List<String> names = new ArrayList<>(); // preferred
ArrayList<String> names = new ArrayList<>(); // less flexible
```
Programming against the interface allows the underlying implementation to be swapped later (e.g., from `ArrayList` to `LinkedList`) with minimal code changes, and makes APIs more flexible for callers (a method accepting a `List<String>` parameter can accept **any** `List` implementation the caller has, not specifically an `ArrayList`) — a core principle of good API design and loose coupling.

### Q101. What is the danger of using mutable objects as keys in a `HashMap`/elements in a `HashSet` (recap and expansion)?
As detailed in Q60, if a key/element's `hashCode()`-relevant fields change after insertion, the object effectively becomes "lost" within the collection (stuck in its original, now-incorrect bucket) — best practice is to use **immutable** objects (or `record`s) as map keys/set elements whenever possible, and if a mutable object absolutely must be used, ensure its hash-relevant fields are never modified while it remains inserted in a hash-based collection.

### Q102. Why should `Collections.sort()`/`Comparator` implementations use `Integer.compare()` rather than subtraction for comparing primitive `int` values?
```java
// Risky — can overflow for large magnitude/opposite-sign values
Comparator<Integer> risky = (a, b) -> a - b;
// Safe
Comparator<Integer> safe = Integer::compare;
```
Subtracting two `int`s can silently **overflow** (e.g., `Integer.MIN_VALUE - 1` wraps around to `Integer.MAX_VALUE`), producing an incorrect sign and therefore an incorrect comparison result for certain input combinations — `Integer.compare()` (and the equivalent methods on other wrapper/primitive types) correctly handles the full range of values without this risk.

### Q103. What is the pitfall of comparing boxed wrapper types (`Integer`, `Long`) with `==` instead of `.equals()`?
```java
Integer a = 127, b = 127;
System.out.println(a == b); // true (both within the cached range)
Integer c = 200, d = 200;
System.out.println(c == d); // false! (outside the default cache range)
```
Java caches/interns boxed `Integer` values in the range **-128 to 127** by default (`Integer.valueOf()`'s internal cache) — meaning `==` comparisons on small boxed integers can *appear* to work correctly by coincidence, while the exact same code pattern silently breaks for larger values, since those aren't guaranteed to be cached/interned. `.equals()` should always be used to compare the logical value of boxed wrapper types, never `==` (which should be reserved for genuine reference-identity comparisons).

### Q104. What is autoboxing/unboxing, and what performance/correctness pitfalls can arise from it in collections?
Autoboxing automatically converts between a primitive type (`int`) and its corresponding wrapper class (`Integer`) as needed — necessary because collections can only hold **objects**, not primitives directly (`List<int>` is not legal Java; `List<Integer>` is required). Pitfalls: repeated autoboxing/unboxing in tight loops introduces meaningful **performance overhead** (object creation, garbage collection pressure) compared to primitive-only code; and unboxing a `null` wrapper value (e.g., a `Map<String, Integer>` lookup returning `null` for a missing key, then attempting to use it in a primitive-context expression) throws a **`NullPointerException`** at the unboxing point, which can be a subtle, easy-to-miss source of bugs.

### Q105. What is the risk of returning a mutable internal collection directly from a method/getter, and how do you avoid it?
```java
// Risky — caller can mutate the internal list directly
public List<String> getItems() { return this.items; }

// Safer
public List<String> getItems() { return Collections.unmodifiableList(this.items); }
// or, for a true independent copy:
public List<String> getItems() { return new ArrayList<>(this.items); }
```
Returning a direct reference to an internal mutable collection breaks **encapsulation** — external code can modify the object's internal state through that reference without going through any of the object's own controlled methods, potentially violating invariants the class was designed to maintain. Returning an unmodifiable view (or a defensive copy, if the caller genuinely needs to mutate a result independently without affecting the original) avoids this.

### Q106. Why should `size() == 0` generally be preferred/replaced by `isEmpty()` when checking whether a collection has any elements?
`isEmpty()` is (or at least can be, and typically is, for well-implemented collections) a more **efficient** and semantically clearer check than `size() == 0` — for some collection types/implementations, computing an exact `size()` could theoretically be more expensive than simply checking for emptiness (though for most standard JDK collections this specific efficiency difference is negligible) — regardless, `isEmpty()` is considered clearer, more idiomatic, self-documenting code regardless of the underlying performance characteristics of a specific implementation.

### Q107. What are common Collections Framework anti-patterns/pitfalls to avoid?
- Using `Vector`/`Hashtable`/`Stack` in new code instead of their modern, non-legacy equivalents.
- Modifying a list directly (`list.remove()`) while iterating with a for-each loop, causing `ConcurrentModificationException`.
- Using mutable objects as `HashMap` keys/`HashSet` elements.
- Comparing boxed wrapper types with `==` instead of `.equals()`.
- Forgetting to override `hashCode()` when overriding `equals()` (or vice versa).
- Using raw types instead of properly parameterized generics.
- Choosing `LinkedList` by default without actually needing its specific access-pattern characteristics (in most cases, `ArrayList`/`ArrayDeque` outperforms it).
- Assuming `Collections.unmodifiableList()` creates a fully independent, immutable copy (it's just a view over a still-mutable backing list).
- Relying on `HashMap`/`HashSet` iteration order, which is unspecified and can change.

### Q108. What are general Java Collections best practices?
- Program against interfaces (`List`, `Map`, `Set`), not concrete implementation classes.
- Choose the right implementation deliberately based on actual access patterns (random access vs. sequential insertion/removal, ordering requirements, thread-safety needs).
- Prefer immutable collections (`List.of()`, defensively copied/unmodifiable views) where mutation isn't genuinely required.
- Correctly and consistently implement `equals()`/`hashCode()` together (or use `record`s/generated code) for any type used as a map key or set element.
- Use `Comparator`'s modern fluent factory methods (`comparing`, `thenComparing`, `reversed`) for building comparison logic.
- Use the appropriate `java.util.concurrent` collection (not a manually synchronized wrapper) for genuinely concurrent access needs.
- Leverage the Streams API for readable, declarative transformations, while being mindful of parallel streams' overhead/correctness trade-offs.
- Set an appropriate initial capacity for large collections built in a known-size loop, to reduce resize overhead.

---

## 17. Miscellaneous & Advanced Topics

### Q109. What is the diamond operator (`<>`), and how did it simplify generic collection instantiation?
Introduced in Java 7, the diamond operator allows the compiler to **infer** the generic type argument from the declared variable type, avoiding redundant repetition:
```java
List<String> names = new ArrayList<>(); // instead of new ArrayList<String>()
```

### Q110. What are `var` (Java 10+) and local variable type inference, and how do they interact with collections?
`var` allows the compiler to infer a local variable's type from its initializer expression, reducing verbosity for collection declarations with obvious/verbose generic types:
```java
var scores = new HashMap<String, List<Integer>>(); // inferred as HashMap<String, List<Integer>>
```
Note `var` only works for **local variables** with an initializer (not fields, method parameters, or return types), and the underlying type is still fully **statically determined** at compile time (it's not dynamic typing) — it's purely a source-code readability/conciseness feature.

### Q111. What is the `Spliterator` interface, and how does it relate to `Iterator` and streams?
A `Spliterator` ("splittable iterator," Java 8+) is the underlying traversal/partitioning mechanism that powers the Streams API's ability to process elements sequentially **or in parallel** — unlike a plain `Iterator` (which only supports sequential, one-element-at-a-time traversal), a `Spliterator` can additionally attempt to **split** itself into two roughly-equal parts (`trySplit()`), enabling a stream's parallel processing infrastructure to divide work across multiple threads.

### Q112. What is `Collections.max()`/`Collections.min()`, and what are their complexity/requirements?
Static utility methods returning the maximum/minimum element of a collection, according to either the elements' natural ordering (`Comparable`) or a supplied `Comparator` — both run in **O(n)** (a full linear scan is required in the general case, since there's no way to know the max/min without examining every element of an arbitrary, unsorted collection).

### Q113. How would you find duplicate elements in a `List` using the Collections Framework?
```java
Set<String> seen = new HashSet<>();
Set<String> duplicates = list.stream()
    .filter(e -> !seen.add(e)) // Set.add() returns false if the element was already present
    .collect(Collectors.toSet());
```
A classic, efficient idiom leveraging `Set.add()`'s boolean return value (indicating whether the element was newly added, i.e., **not** already present) as a compact way to detect duplicates in a single linear pass.

### Q114. How do you convert between an array and a `List`, and what pitfalls exist?
```java
List<String> list = Arrays.asList(array); // fixed-size view, backed directly by the array (see Q73)
List<String> mutableList = new ArrayList<>(Arrays.asList(array)); // fully independent, mutable copy
String[] array2 = list.toArray(new String[0]); // List back to a properly-typed array
```
A common pitfall: forgetting that `Arrays.asList()` returns a fixed-size list still backed by the original array (Q73) — calling `add()`/`remove()` on it throws `UnsupportedOperationException`, surprising developers expecting a fully mutable, independent `ArrayList`-like result.

### Q115. What is the difference between `Set.of()`'s duplicate-handling behavior and a regular `Set`'s `add()` behavior?
```java
Set<String> s = Set.of("a", "b", "a"); // throws IllegalArgumentException at creation time
Set<String> s2 = new HashSet<>();
s2.add("a"); s2.add("b"); s2.add("a"); // silently ignored — add() just returns false for the duplicate
```
`Set.of(...)` (the immutable factory method) **throws an exception immediately** if duplicate elements are passed in during construction — a stricter, fail-fast behavior distinct from a regular mutable `Set`'s `add()` method, which simply and silently returns `false` for an already-present element without throwing anything, reflecting `Set.of()`'s general design philosophy of catching likely programming mistakes early/explicitly wherever it reasonably can.

---

*This guide covers the core Java Collections Framework interview surface area. For role-specific deep dives (e.g., JVM internals affecting collection performance, deep red-black tree rotation mechanics, or building fully custom collection implementations from scratch), further topic-specific preparation is recommended.*
