# Java Full Stack Developer – Interview Answers (3 Years Experience Level)

**Role:** Java Full Stack Developer · Kaiser Permanente GCC, Pune
**How to use:** Every question has a **Short** answer (say it in one breath, about 10 seconds) and a **Brief** answer (the 30–60 second explanation an interviewer expects from someone with 3 years of experience). Where possible, connect the brief answer to your own project.

## 1. Core Java Basics

### 1. What are JDK, JRE, and JVM, and how do they differ?
**Short:** JVM runs bytecode, JRE = JVM + libraries to run Java, JDK = JRE + development tools like javac.

**Brief:** The JVM is the engine that loads, verifies and executes bytecode and manages memory. The JRE packages the JVM with the core class libraries, which is all you need to run an application. The JDK adds development tools such as the compiler (javac), debugger, jar and javadoc. Since Java 11 there is no separate JRE download; we install a JDK on both dev machines and build images.

### 2. Why is Java platform-independent? Is the JVM platform-independent?
**Short:** Java compiles to bytecode that any JVM can run; the JVM itself is platform-specific.

**Brief:** javac produces .class files containing bytecode, not native machine code. Each OS has its own JVM implementation that translates that bytecode into native instructions, so the same jar runs on Windows, Linux or Mac — "write once, run anywhere". The JVM itself is platform-dependent because it must talk to the underlying OS and hardware.

### 3. Explain JVM architecture: class loader, runtime data areas, and execution engine.
**Short:** Class loader loads classes, runtime data areas store data, and the execution engine runs the code.

**Brief:** The class loader subsystem loads, links (verify, prepare, resolve) and initializes classes using the Bootstrap, Platform and Application loaders with parent delegation. Runtime data areas are the Heap and Metaspace (shared by all threads) plus the Stack, PC register and native method stack (one per thread). The execution engine has the interpreter, the JIT compiler that compiles hot code to native code, and the garbage collector.

### 4. What is the difference between stack and heap memory?
**Short:** Stack holds method frames and local variables per thread; heap holds objects shared by all threads.

**Brief:** Each thread has its own stack storing method call frames, local primitives and object references; it is fast, LIFO and freed automatically when a method returns. The heap stores all objects and arrays, is shared across threads and is cleaned by the garbage collector. A deep recursion gives StackOverflowError, while too many live objects give OutOfMemoryError: Java heap space.

### 5. What is the String pool? Why is String immutable?
**Short:** The String pool is a heap area that reuses string literals; String is immutable for safety, caching and security.

**Brief:** When you write `String s = "abc"`, the JVM checks the pool and reuses an existing object, saving memory; `new String("abc")` creates a separate heap object. Immutability makes pooling safe, makes Strings thread-safe without locks, allows the hashCode to be cached (great for HashMap keys) and protects security-sensitive values like file paths, URLs and class names from being changed after validation.

### 6. Compare String, StringBuilder, and StringBuffer.
**Short:** String is immutable; StringBuilder is mutable and fast; StringBuffer is mutable and synchronized.

**Brief:** Every change to a String creates a new object, so concatenation in a loop is wasteful. StringBuilder modifies the same buffer and is the default choice for building strings in a single thread. StringBuffer has the same API but its methods are synchronized, making it thread-safe but slower; in practice it is rarely used today.

### 7. What is the difference between `==` and `.equals()`?
**Short:** `==` compares references (or primitive values); `.equals()` compares logical content.

**Brief:** For objects, `==` checks whether two references point to the same object in memory. `equals()` is defined in Object (same as `==` by default) but classes like String, Integer and our entities override it to compare values. For example, two `new String("a")` objects are not `==` but are `equals()`. Always use equals for strings and wrapper objects.

### 8. What is the hashCode/equals contract? What breaks if you override only one?
**Short:** Equal objects must have equal hash codes; overriding only equals breaks hash-based collections.

**Brief:** The contract says if `a.equals(b)` then `a.hashCode() == b.hashCode()`, while unequal objects may still share a hash code. If you override equals but not hashCode, two "equal" objects land in different HashMap buckets, so `map.get()` fails and HashSet allows duplicates. In projects I generate both with the IDE, Lombok `@EqualsAndHashCode`, or use records.

### 9. What are `final`, `finally`, and `finalize`?
**Short:** final is a modifier, finally is a block that always runs, finalize is a deprecated GC callback.

**Brief:** A final variable cannot be reassigned, a final method cannot be overridden, and a final class cannot be extended. finally follows try/catch and runs whether or not an exception occurs, used for cleanup. finalize() was called by GC before reclaiming an object but is unpredictable and deprecated since Java 9; we use try-with-resources or Cleaner instead.

### 10. What does the `static` keyword do? Can you override a static method?
**Short:** static means belonging to the class rather than an instance; static methods can be hidden but not overridden.

**Brief:** Static variables are shared across all instances, static methods can be called without an object, static blocks run once at class loading, and static nested classes do not need an outer instance. If a subclass declares the same static method, it is method hiding: the version called depends on the reference type at compile time, not the runtime object, so there is no polymorphism.

### 11. What are wrapper classes, autoboxing, and unboxing? Explain the Integer cache.
**Short:** Wrappers are object versions of primitives; autoboxing converts automatically; Integer caches -128 to 127.

**Brief:** Collections cannot store primitives, so we use Integer, Long, Double and so on. Autoboxing converts `int` to `Integer` automatically and unboxing does the reverse; unboxing a null throws NullPointerException. `Integer.valueOf()` returns cached objects for -128 to 127, so `Integer a = 127, b = 127; a == b` is true, but for 128 it is false. That is why wrappers must be compared with equals().

### 12. Is Java pass-by-value or pass-by-reference?
**Short:** Always pass-by-value; for objects, the value of the reference is copied.

**Brief:** For primitives the actual value is copied. For objects, a copy of the reference is passed, so the method can modify the object's state through it, but reassigning the parameter to a new object does not affect the caller's variable. That proves it is pass-by-value of the reference.

### 13. What is the difference between shallow copy and deep copy, and how does `clone()` work?
**Short:** Shallow copy shares nested objects; deep copy duplicates them too.

**Brief:** `Object.clone()` (with Cloneable) does a field-by-field shallow copy, so nested objects like a List are shared between original and copy. A deep copy also clones nested objects, so changes do not leak. In practice clone is avoided; we prefer copy constructors, static factory methods, or mapping libraries like MapStruct.

### 14. How do you create an immutable class?
**Short:** Make the class final, fields private final, no setters, and defensively copy mutable fields.

**Brief:** Declare the class final so it cannot be subclassed, make all fields private final and set them only in the constructor, and provide getters only. For mutable fields like List or Date, copy them in the constructor and return copies or unmodifiable views from getters (e.g. `List.copyOf`). Since Java 16, records give most of this automatically.

### 15. What are serialization, `transient`, and `serialVersionUID`?
**Short:** Serialization converts an object to bytes; transient fields are skipped; serialVersionUID is the version check.

**Brief:** A class implementing Serializable can be written with ObjectOutputStream and restored later. Fields marked transient (like passwords or derived values) are not serialized and come back as default values. serialVersionUID identifies the class version; if it does not match during deserialization you get InvalidClassException, so we declare it explicitly. In REST services we mostly use JSON serialization with Jackson instead.

### 16. What are the access modifiers, and what is the default access level?
**Short:** private, default (package-private), protected and public; default is package-level.

**Brief:** private is visible only inside the class; default (no keyword) is visible within the same package; protected adds subclasses in other packages; public is visible everywhere. Good practice is to keep fields private and expose the minimum needed, which supports encapsulation.

### 17. How does garbage collection work? Name the types of GC.
**Short:** GC automatically frees unreachable objects, using generations; common collectors are G1, ZGC, Parallel and Serial.

**Brief:** GC finds objects not reachable from GC roots (stack variables, static fields, active threads) and reclaims them. The heap is generational: new objects go to Young Gen (Eden and Survivor spaces) where minor GCs are frequent; long-lived objects are promoted to Old Gen. G1 is the default since Java 9 and balances throughput and pause times; ZGC and Shenandoah give very low pauses for large heaps; Parallel GC maximizes throughput.

### 18. What are strong, weak, soft, and phantom references?
**Short:** They control how eagerly GC may collect an object, from never (strong) to always (weak/phantom).

**Brief:** A normal reference is strong, so the object is never collected while reachable. SoftReference objects are cleared only when memory is low, useful for caches. WeakReference objects are cleared at the next GC, used by WeakHashMap. PhantomReference is enqueued after the object is finalized and is used for cleanup tracking.

### 19. What are the causes of memory leaks in Java, and how do you detect them?
**Short:** Objects stay referenced unintentionally; detect them with heap dumps and profilers.

**Brief:** Common causes are static collections that keep growing, caches without eviction, unclosed resources, listeners not removed, ThreadLocal values not cleared in thread pools, and bad equals/hashCode in map keys. Symptoms are heap growing after each GC and eventually OOM. I would take a heap dump (jmap or `-XX:+HeapDumpOnOutOfMemoryError`) and analyse it in Eclipse MAT or VisualVM to find the dominator objects.

### 20. What is the difference between OutOfMemoryError and StackOverflowError?
**Short:** OOM means the heap (or metaspace) is full; StackOverflowError means a thread's stack is full.

**Brief:** StackOverflowError usually comes from infinite or very deep recursion, as each call adds a frame to the thread stack. OutOfMemoryError happens when the heap cannot allocate more objects even after GC, usually due to leaks or large data loads, and it can also be Metaspace or "unable to create new native thread". Both are Errors, not Exceptions, and should not normally be caught.

## 2. OOP Concepts

### 1. What are the four pillars of OOP? Give a real-world example of each.
**Short:** Encapsulation, Abstraction, Inheritance and Polymorphism.

**Brief:** Encapsulation hides data behind methods, like a Patient class with private fields and validated setters. Abstraction exposes what an object does, not how, like a PaymentService interface hiding the gateway logic. Inheritance reuses behaviour, like Doctor and Nurse extending Employee. Polymorphism lets one interface have many forms, like `Notification.send()` behaving differently for Email and SMS implementations.

### 2. What is the difference between an abstract class and an interface after Java 8?
**Short:** Abstract classes can hold state and constructors; interfaces define contracts and allow multiple implementation.

**Brief:** Since Java 8, interfaces can have default and static methods (and private methods since Java 9), but they still cannot have instance fields or constructors — only constants. Abstract classes can have state, constructors and any access modifiers, but a class can extend only one. I use an interface for a capability or contract (e.g. Repository) and an abstract class when related classes share common state and code (e.g. BaseEntity with id and audit fields).

### 3. Compare method overloading and overriding. Can you overload by return type only?
**Short:** Overloading = same name, different parameters, same class; overriding = same signature in a subclass. Return type alone is not enough.

**Brief:** Overloading is resolved at compile time and requires a different parameter list; changing only the return type gives a compile error because the call would be ambiguous. Overriding happens in a subclass with the same signature, resolved at runtime, and must not reduce visibility or throw broader checked exceptions. Use `@Override` to let the compiler verify it.

### 4. Compare compile-time and runtime polymorphism.
**Short:** Compile-time is method overloading; runtime is method overriding via dynamic dispatch.

**Brief:** With overloading, the compiler picks the method based on the argument types. With overriding, the JVM decides at runtime based on the actual object type, e.g. `Shape s = new Circle(); s.area()` calls Circle's area. Runtime polymorphism is what enables Spring to inject different implementations behind one interface.

### 5. Why does Java not support multiple inheritance with classes? How do interfaces solve the diamond problem?
**Short:** To avoid ambiguity (diamond problem); interfaces force the class to resolve conflicts explicitly.

**Brief:** If class C extended both A and B, which both define `show()`, the JVM would not know which to inherit. Java allows multiple interfaces instead; if two interfaces provide the same default method, the implementing class must override it and can choose explicitly with `A.super.show()`. Class methods also always win over interface defaults.

### 6. When should you use composition instead of inheritance?
**Short:** When the relationship is "has-a" rather than "is-a", or when you want flexibility.

**Brief:** Inheritance creates tight coupling: changes in the parent can break children, and you inherit everything. Composition means holding a reference to another object and delegating to it, which is easier to test, swap and mock. For example, an OrderService has a PaymentService rather than extending it. "Favor composition over inheritance" is a core design principle, and Spring DI is built on it.

### 7. What are covariant return types?
**Short:** An overriding method can return a subtype of the parent method's return type.

**Brief:** If `Animal getAnimal()` is in the parent, a subclass may override it as `Dog getAnimal()`. This avoids casting for callers using the subclass and is commonly seen in builder patterns and `clone()` overrides.

### 8. Can a constructor be private? When would you use one?
**Short:** Yes — for Singletons, utility classes and factory methods.

**Brief:** A private constructor prevents creating instances from outside. It is used in the Singleton pattern, in utility classes with only static methods (like Collections), and when you want to force object creation through static factory methods or a Builder for better naming and validation.

### 9. Explain constructor chaining with `this()` and `super()`.
**Short:** `this()` calls another constructor of the same class; `super()` calls the parent constructor.

**Brief:** Chaining avoids duplicate initialisation code, e.g. a no-arg constructor calling `this("default")`. Either call must be the first statement, so you cannot use both in one constructor. If you do not write `super()`, the compiler inserts a no-arg `super()` automatically, which fails if the parent has no no-arg constructor.

### 10. What are the SOLID principles? Explain each with an example.
**Short:** Single responsibility, Open/closed, Liskov substitution, Interface segregation and Dependency inversion.

**Brief:** S — a class has one reason to change (separate Controller, Service and Repository). O — open for extension, closed for modification (add a new PaymentStrategy without editing existing code). L — subclasses must be usable wherever the parent is expected without breaking behaviour. I — prefer small specific interfaces over one big one. D — depend on abstractions, not concrete classes, which Spring achieves by injecting interfaces.

### 11. Explain association, aggregation, and composition.
**Short:** Association is any relationship; aggregation is weak "has-a"; composition is strong "owns-a".

**Brief:** Association is a general link, like Doctor and Patient. Aggregation is a has-a where the part can exist independently, like a Department and its Employees. Composition is ownership where the part's lifecycle depends on the whole, like an Order and its OrderItems — delete the order and the items go too. In JPA this maps to cascade ALL with orphanRemoval.

### 12. Can you override a private or final method?
**Short:** No — private methods are not inherited and final methods cannot be overridden.

**Brief:** A private method is invisible to subclasses; declaring the same method in the child creates a new, unrelated method. A final method gives a compile error if you try to override it. Static methods are hidden, not overridden.
## 3. Collections Framework

### 1. Describe the Collection hierarchy. Why does Map not extend Collection?
**Short:** Iterable → Collection → List, Set, Queue; Map is separate because it stores key-value pairs.

**Brief:** List (ArrayList, LinkedList) keeps order and allows duplicates; Set (HashSet, LinkedHashSet, TreeSet) disallows duplicates; Queue/Deque (PriorityQueue, ArrayDeque) supports FIFO or priority processing. Map (HashMap, LinkedHashMap, TreeMap) stores pairs, and methods like `add(E)` do not fit a key-value structure, so it has its own hierarchy. You can still view a map as collections via `keySet()`, `values()` and `entrySet()`.

### 2. Compare ArrayList and LinkedList, and say when to use each.
**Short:** ArrayList is a dynamic array with fast random access; LinkedList is a doubly linked list with cheap insert/delete at the ends.

**Brief:** ArrayList gives O(1) `get(index)` and amortised O(1) append, but inserting in the middle shifts elements, O(n). LinkedList gives O(1) add/remove at head or tail but O(n) access by index, and uses more memory per node. In practice ArrayList is the default for almost everything thanks to cache-friendly memory; for queue behaviour I use ArrayDeque rather than LinkedList.

### 3. Compare ArrayList and Vector, and Array and ArrayList.
**Short:** Vector is a legacy synchronized ArrayList; arrays are fixed-size, ArrayList is resizable.

**Brief:** Vector synchronizes every method and grows by 100%, while ArrayList is unsynchronized and grows by 50%; for thread safety we use CopyOnWriteArrayList or `Collections.synchronizedList` instead. Arrays have fixed length, can hold primitives and are slightly faster; ArrayList resizes automatically, stores only objects and offers rich API methods and generics.

### 4. How does HashMap work internally?
**Short:** An array of buckets; the key's hash picks the bucket, collisions are chained in a list or a red-black tree.

**Brief:** On `put`, HashMap computes `hashCode()`, spreads the bits (`h ^ (h >>> 16)`) and finds the index with `(n - 1) & hash`. If the bucket is empty it stores a Node; otherwise it walks the chain, using `equals()` to replace a matching key or append a new node. Since Java 8, a bucket with more than 8 nodes (and table size ≥ 64) becomes a red-black tree, so worst-case lookup is O(log n). Default capacity is 16 and load factor 0.75: when size exceeds 12, the table doubles and entries are rehashed. HashMap allows one null key (bucket 0) and is not thread-safe.

### 5. What happens when two keys have the same hashCode?
**Short:** It is a collision; both go into the same bucket and equals() distinguishes them.

**Brief:** Both entries land in the same bucket, stored as a linked list (or tree after the threshold). On `get`, HashMap finds the bucket by hash, then compares each key with `equals()` to return the correct value. If equals says they are equal, the second put simply overwrites the value. Many collisions degrade performance, which is why a good hashCode matters.

### 6. Compare HashMap, Hashtable, ConcurrentHashMap, and SynchronizedMap.
**Short:** HashMap is not thread-safe; Hashtable and synchronizedMap lock the whole map; ConcurrentHashMap allows concurrent access with fine-grained locking.

**Brief:** HashMap is fastest for single-threaded use and allows null keys and values. Hashtable is legacy, fully synchronized and disallows nulls. `Collections.synchronizedMap` wraps a map with one lock, so only one thread works at a time and iteration needs manual synchronization. ConcurrentHashMap locks only per bucket for writes, reads are lock-free, it disallows nulls and has atomic methods like `computeIfAbsent` and `merge`, so it is the choice for shared caches.

### 7. How does ConcurrentHashMap work internally?
**Short:** Java 7 used segment locks; Java 8+ uses CAS for empty buckets and synchronized on the bucket's first node.

**Brief:** In Java 7 the map was split into 16 segments, each a separately locked mini hash table. Java 8 removed segments: inserting into an empty bucket uses a lock-free CAS operation, and for a non-empty bucket it synchronizes only on that bucket's head node. Reads use volatile access and do not lock. Resizing is done cooperatively by multiple threads, and iterators are weakly consistent, so they never throw ConcurrentModificationException.

### 8. Compare HashMap, LinkedHashMap, and TreeMap.
**Short:** HashMap has no order; LinkedHashMap keeps insertion (or access) order; TreeMap keeps keys sorted.

**Brief:** HashMap gives O(1) average operations. LinkedHashMap adds a doubly linked list across entries to preserve insertion order, or access order when constructed with `accessOrder=true`, which is the basis for an LRU cache. TreeMap is a red-black tree with O(log n) operations, sorts keys naturally or by a Comparator, does not allow null keys, and offers navigation methods like `floorKey` and `headMap`.

### 9. Compare HashSet, LinkedHashSet, and TreeSet. How does HashSet work internally?
**Short:** Same as their Map counterparts — HashSet is backed by a HashMap.

**Brief:** HashSet stores elements as keys in an internal HashMap with a dummy constant value, so uniqueness relies on hashCode and equals. LinkedHashSet preserves insertion order using LinkedHashMap. TreeSet uses TreeMap, keeps elements sorted and relies on compareTo/compare rather than equals for uniqueness.

### 10. Compare Comparable and Comparator.
**Short:** Comparable defines natural ordering inside the class; Comparator defines external, multiple orderings.

**Brief:** Comparable has `compareTo(T o)` implemented by the class itself (e.g. String, Integer), giving one default sort. Comparator has `compare(a, b)` in a separate object, so you can sort the same class many ways without modifying it. With Java 8 I write `Comparator.comparing(Employee::getSalary).thenComparing(Employee::getName)`.

### 11. Compare fail-fast and fail-safe iterators, and explain ConcurrentModificationException.
**Short:** Fail-fast iterators throw CME on concurrent modification; fail-safe ones work on a copy or snapshot.

**Brief:** ArrayList and HashMap iterators track a `modCount`; if the collection is structurally changed outside the iterator during iteration (even in the same thread, like removing in a for-each), they throw ConcurrentModificationException. To remove safely, use `iterator.remove()` or `removeIf()`. Concurrent collections like CopyOnWriteArrayList and ConcurrentHashMap have fail-safe or weakly consistent iterators that never throw CME.

### 12. How do you make a collection read-only or synchronized?
**Short:** Use `Collections.unmodifiableList` / `List.of` for read-only and `Collections.synchronizedList` for synchronized.

**Brief:** `Collections.unmodifiableXxx()` returns a read-only view (changes to the original are still visible), while `List.of()` and `List.copyOf()` create truly immutable lists that also reject nulls. `Collections.synchronizedXxx()` wraps every method with a lock, but you must synchronize manually when iterating. For real concurrency, the java.util.concurrent collections are better.

### 13. Why should HashMap keys be immutable? What happens with a mutable key?
**Short:** If a key's hash changes after insertion, the entry becomes unreachable.

**Brief:** HashMap stores the entry in the bucket computed from the hash at put time. If you mutate fields used in hashCode afterwards, `get()` looks in a different bucket and returns null, while the old entry remains, causing data loss and a memory leak. That is why String, Integer and records are ideal keys.

### 14. Explain PriorityQueue, Deque, and ArrayDeque.
**Short:** PriorityQueue orders elements by priority; Deque is a double-ended queue; ArrayDeque is its fast array implementation.

**Brief:** PriorityQueue is a binary min-heap: `poll()` returns the smallest (or by Comparator) element in O(log n), useful for top-K problems and scheduling. Deque supports insertion and removal at both ends, so it works as a stack or queue. ArrayDeque is a resizable circular array, faster than Stack and LinkedList, and does not allow nulls.

### 15. Explain the internals of CopyOnWriteArrayList.
**Short:** Every write creates a new copy of the array; reads use the current snapshot without locks.

**Brief:** Write operations take a lock, copy the internal array, modify the copy and swap the reference. Readers and iterators work on the snapshot taken when they started, so they never throw CME. It is ideal when reads vastly outnumber writes, like a list of event listeners or configuration, but expensive for frequent writes on large lists.

### 16. How do you sort a HashMap by value?
**Short:** Stream the entrySet, sort with `Map.Entry.comparingByValue()`, and collect into a LinkedHashMap.

**Brief:** HashMap has no order, so we create a new ordered map:

```java
Map<String, Integer> sorted = map.entrySet().stream()
    .sorted(Map.Entry.comparingByValue(Comparator.reverseOrder()))
    .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue,
             (a, b) -> a, LinkedHashMap::new));
```

### 17. Compare WeakHashMap and IdentityHashMap.
**Short:** WeakHashMap holds keys weakly so GC can remove entries; IdentityHashMap compares keys with == instead of equals.

**Brief:** In WeakHashMap, once a key has no strong references elsewhere, GC collects it and the entry disappears — useful for metadata caches. IdentityHashMap uses reference equality and `System.identityHashCode`, which is useful for object graph traversal, serialization or proxies, where two equal but distinct objects must be treated separately.

## 4. Exception Handling

### 1. Describe the exception hierarchy: Throwable, Error, and Exception.
**Short:** Throwable is the root; Error is for serious JVM problems; Exception is for recoverable application problems.

**Brief:** Throwable has two branches. Error (OutOfMemoryError, StackOverflowError) signals problems the application should not try to handle. Exception covers conditions we can handle; RuntimeException and its subclasses are unchecked, while other Exceptions such as IOException and SQLException are checked.

### 2. Compare checked and unchecked exceptions, with examples.
**Short:** Checked exceptions are verified at compile time; unchecked ones (RuntimeException) are not.

**Brief:** Checked exceptions like IOException and SQLException must be caught or declared with throws, and represent recoverable external conditions. Unchecked exceptions like NullPointerException, IllegalArgumentException and ArrayIndexOutOfBoundsException usually indicate programming bugs or invalid input. Spring prefers unchecked exceptions — for example, it translates SQLException into DataAccessException — so business code stays clean.

### 3. Compare `throw` and `throws`.
**Short:** `throw` actually throws an exception object; `throws` declares exceptions a method may throw.

**Brief:** `throw new IllegalArgumentException("Invalid id")` is a statement inside a method body and throws one instance. `throws IOException, SQLException` is part of the method signature and tells callers which checked exceptions they must handle.

### 4. Explain try-with-resources and AutoCloseable.
**Short:** It automatically closes resources that implement AutoCloseable at the end of the try block.

**Brief:** Introduced in Java 7, `try (Connection c = ds.getConnection(); PreparedStatement ps = ...) { }` closes resources in reverse order even if an exception occurs, removing the need for finally blocks. If both the body and close() throw, the close exception is added as a suppressed exception, available via `getSuppressed()`.

### 5. Can `finally` be skipped? Does it run if there is a return in try?
**Short:** finally runs even after return; it is skipped only on `System.exit()`, JVM crash or a killed thread.

**Brief:** If try has a return, finally still executes before the method actually returns. A return inside finally overrides the try's return value and swallows exceptions, so it is bad practice. finally does not run if the JVM exits via `System.exit()`, crashes, or the machine loses power.

### 6. How do you create a custom exception, and should it be checked or unchecked?
**Short:** Extend RuntimeException (usually) or Exception and add constructors; prefer unchecked for business errors.

**Brief:** For example, `class PatientNotFoundException extends RuntimeException { public PatientNotFoundException(String msg) { super(msg); } }`. In Spring Boot I make business exceptions unchecked, since they work naturally with `@Transactional` rollback (which by default rolls back only on unchecked exceptions) and are mapped to HTTP responses in a `@ControllerAdvice`, e.g. 404 for not found.

### 7. What are the rules for exceptions when overriding methods?
**Short:** An overriding method cannot throw new or broader checked exceptions.

**Brief:** If the parent method declares IOException, the child can declare IOException, a subclass like FileNotFoundException, or nothing — but not Exception. If the parent declares no checked exceptions, the child cannot add any. Unchecked exceptions have no restriction.

### 8. What are exception chaining and multi-catch?
**Short:** Chaining wraps a cause inside a new exception; multi-catch handles several types in one catch block.

**Brief:** Chaining preserves the root cause: `throw new ServiceException("Failed to save patient", e)`, so the full stack trace is logged. Multi-catch, `catch (IOException | SQLException e)`, reduces duplicate code; the types must not be subclasses of each other and the variable is effectively final.

### 9. What are the best practices for exception handling in a real project?
**Short:** Catch specific exceptions, never swallow them, log once, and map them to clean API responses.

**Brief:** Do not catch generic Exception or Throwable unless at a boundary; never leave an empty catch block; log with context and the stack trace once (not at every layer); wrap low-level exceptions into meaningful business exceptions; use a global `@ControllerAdvice` to return consistent error JSON with the right status codes; and never expose stack traces or sensitive data (like patient info) to clients.
## 5. Java 8+ Features

### 1. What are lambda expressions and functional interfaces?
**Short:** A lambda is an anonymous function; a functional interface has exactly one abstract method.

**Brief:** Lambdas like `(a, b) -> a + b` provide implementations of functional interfaces concisely, replacing anonymous inner classes. A functional interface (Runnable, Comparator, Predicate) has a single abstract method and can be marked `@FunctionalInterface` so the compiler enforces it. Default and static methods do not count toward that single method.

### 2. Explain Predicate, Function, Consumer, Supplier, and BiFunction.
**Short:** Predicate tests, Function transforms, Consumer accepts, Supplier provides, BiFunction takes two inputs.

**Brief:** `Predicate<T>` has `boolean test(T)`, used in `filter`. `Function<T,R>` has `R apply(T)`, used in `map`. `Consumer<T>` has `void accept(T)`, used in `forEach`. `Supplier<T>` has `T get()`, used in `orElseGet` and lazy creation. `BiFunction<T,U,R>` has `R apply(T, U)`. Primitive versions like IntPredicate avoid boxing.

### 3. What are method references, and what are their types?
**Short:** A shorthand for a lambda that calls an existing method, written with `::`.

**Brief:** There are four types: static method (`Integer::parseInt`), instance method of a particular object (`System.out::println`), instance method of an arbitrary object of a type (`String::toUpperCase`), and constructor reference (`ArrayList::new`). They make stream code more readable.

### 4. What is the Stream API? Compare intermediate and terminal operations.
**Short:** Streams process collections declaratively; intermediate operations return a stream, terminal operations produce a result.

**Brief:** A stream is a pipeline: source → intermediate operations → terminal operation. Intermediate operations (filter, map, sorted, distinct, limit) are lazy and return a new stream. Terminal operations (collect, forEach, reduce, count, findFirst) trigger execution and close the stream, so a stream cannot be reused. Streams do not modify the source collection.

### 5. Compare `map` and `flatMap`.
**Short:** map transforms each element one-to-one; flatMap transforms each element into a stream and flattens them.

**Brief:** `map(String::length)` turns Stream<String> into Stream<Integer>. flatMap is used when each element produces multiple values, for example `orders.stream().flatMap(o -> o.getItems().stream())` gives one stream of all items instead of Stream<List<Item>>. Optional also has flatMap to avoid nested Optionals.

### 6. Compare streams and collections, and sequential and parallel streams.
**Short:** Collections store data; streams compute on data. Parallel streams split work across ForkJoinPool threads.

**Brief:** A collection is an in-memory data structure that can be iterated many times; a stream is a one-time, lazy view for processing. Parallel streams use the common ForkJoinPool and can speed up CPU-heavy work on large datasets, but they add overhead, need thread-safe and stateless operations, and should not be used for I/O or inside web requests where they compete for shared threads. I default to sequential streams.

### 7. Explain lazy evaluation in streams.
**Short:** Intermediate operations run only when a terminal operation is called, element by element.

**Brief:** Without a terminal operation, nothing executes. When it does run, elements flow through the whole pipeline one at a time rather than stage by stage, which allows short-circuiting: in `filter(...).findFirst()`, processing stops as soon as the first match is found, even on huge or infinite streams.

### 8. What are Optional's methods, and what are its best practices?
**Short:** Optional wraps a possibly-null value; `orElse` always evaluates its argument, `orElseGet` only when empty.

**Brief:** Common methods are `of`, `ofNullable`, `empty`, `isPresent`, `ifPresent`, `map`, `filter`, `orElse`, `orElseGet` and `orElseThrow`. `orElse(createDefault())` calls createDefault even if a value exists, so use `orElseGet(() -> createDefault())` for expensive defaults. Use Optional as a return type (like Spring Data `findById`), not for fields, parameters or collections, and avoid calling `get()` without a check.

### 9. What are default and static methods in interfaces?
**Short:** Default methods have a body and are inherited; static methods belong to the interface itself.

**Brief:** Default methods were added so interfaces could evolve without breaking existing implementations — for example `List.sort` and `Collection.stream`. Implementing classes may override them. Static interface methods are utility methods called as `InterfaceName.method()` and are not inherited.

### 10. What are the new Date/Time API classes?
**Short:** java.time provides immutable, thread-safe classes like LocalDate, LocalDateTime, ZonedDateTime, Instant and Duration.

**Brief:** The old Date and SimpleDateFormat were mutable and not thread-safe. LocalDate holds a date, LocalTime a time and LocalDateTime both without a zone; ZonedDateTime and OffsetDateTime include a time zone; Instant is a UTC timestamp; Period and Duration measure date and time amounts; DateTimeFormatter is thread-safe. For a US–India team, storing UTC (Instant) and converting at the UI is important.

### 11. Explain `Collectors.groupingBy`, `partitioningBy`, `toMap`, and `joining`.
**Short:** groupingBy groups into a Map by key, partitioningBy splits into true/false, toMap builds a map, joining concatenates strings.

**Brief:** `groupingBy(Employee::getDept, counting())` gives a count per department and accepts downstream collectors like `averagingDouble` or `maxBy`. `partitioningBy(e -> e.getSalary() > 50000)` returns `Map<Boolean, List<Employee>>`. `toMap(keyFn, valueFn)` throws IllegalStateException on duplicate keys unless you pass a merge function. `joining(", ", "[", "]")` concatenates strings with a delimiter, prefix and suffix.

### 12. What are `reduce`, `findFirst`, `anyMatch`, `distinct`, and `sorted`?
**Short:** reduce combines elements into one, findFirst returns the first, anyMatch tests any, distinct removes duplicates, sorted orders.

**Brief:** `reduce(0, Integer::sum)` folds elements into a single result. `findFirst()` returns an Optional of the first element (findAny is faster in parallel). `anyMatch`, `allMatch` and `noneMatch` are short-circuiting boolean checks. `distinct()` uses equals/hashCode. `sorted()` uses natural order or a supplied Comparator and is a stateful operation.

### 13. What features came after Java 8?
**Short:** var (10), switch expressions (14), text blocks (15), records (16), sealed classes (17) and virtual threads (21).

**Brief:** `var` gives local variable type inference. Switch expressions return values using arrow syntax. Text blocks (`"""`) make multi-line SQL and JSON readable. Records are immutable data carriers with auto-generated constructor, accessors, equals, hashCode and toString — great for DTOs. Sealed classes restrict which classes can extend a type. Java 17 and 21 are LTS; Java 21 added virtual threads for massive lightweight concurrency, plus pattern matching for switch and record patterns. Spring Boot 3 requires Java 17+.

### Common Stream Coding Questions

### 1. Find duplicate elements in a list.
**Short:** Use a Set's add() result inside a filter, or group and count.

**Brief:**
```java
Set<Integer> seen = new HashSet<>();
List<Integer> dups = list.stream().filter(n -> !seen.add(n)).distinct().toList();
// or
list.stream().collect(Collectors.groupingBy(Function.identity(), Collectors.counting()))
    .entrySet().stream().filter(e -> e.getValue() > 1).map(Map.Entry::getKey).toList();
```

### 2. Find the second-highest number or salary.
**Short:** Distinct, sort descending, skip 1, findFirst.

**Brief:**
```java
Optional<Integer> second = nums.stream().distinct()
    .sorted(Comparator.reverseOrder()).skip(1).findFirst();
Optional<Employee> secondEmp = emps.stream()
    .sorted(Comparator.comparingDouble(Employee::getSalary).reversed())
    .skip(1).findFirst();
```

### 3. Count the frequency of each character in a string.
**Short:** Stream the chars and groupingBy with counting.

**Brief:**
```java
Map<Character, Long> freq = str.chars().mapToObj(c -> (char) c)
    .collect(Collectors.groupingBy(Function.identity(), LinkedHashMap::new, Collectors.counting()));
```

### 4. Group employees by department and find the highest salary per department.
**Short:** groupingBy department with a maxBy downstream collector.

**Brief:**
```java
Map<String, Optional<Employee>> top = emps.stream().collect(Collectors.groupingBy(
    Employee::getDept, Collectors.maxBy(Comparator.comparingDouble(Employee::getSalary))));
```

### 5. Find the first non-repeating character.
**Short:** Count with a LinkedHashMap to keep order, then pick the first entry with count 1.

**Brief:**
```java
Character first = str.chars().mapToObj(c -> (char) c)
    .collect(Collectors.groupingBy(Function.identity(), LinkedHashMap::new, Collectors.counting()))
    .entrySet().stream().filter(e -> e.getValue() == 1)
    .map(Map.Entry::getKey).findFirst().orElse(null);
```

### 6. Sort employees by salary, then by name.
**Short:** Use Comparator.comparing with thenComparing.

**Brief:**
```java
List<Employee> sorted = emps.stream()
    .sorted(Comparator.comparingDouble(Employee::getSalary)
        .thenComparing(Employee::getName)).toList();
```

### 7. Join a list of strings with a comma.
**Short:** `Collectors.joining(", ")` or `String.join`.

**Brief:** `String result = names.stream().collect(Collectors.joining(", "));` — or simply `String.join(", ", names)`.

### 8. Find the average salary by department.
**Short:** groupingBy department with averagingDouble.

**Brief:**
```java
Map<String, Double> avg = emps.stream().collect(
    Collectors.groupingBy(Employee::getDept, Collectors.averagingDouble(Employee::getSalary)));
```

### 9. Separate even and odd numbers.
**Short:** partitioningBy with `n % 2 == 0`.

**Brief:** `Map<Boolean, List<Integer>> parts = nums.stream().collect(Collectors.partitioningBy(n -> n % 2 == 0));` — `parts.get(true)` gives evens and `parts.get(false)` gives odds.

### 10. Convert a List to a Map, and handle duplicate keys.
**Short:** Collectors.toMap with a merge function.

**Brief:**
```java
Map<Long, Employee> byId = emps.stream()
    .collect(Collectors.toMap(Employee::getId, Function.identity(), (e1, e2) -> e1));
```
Without the third argument, duplicate keys throw IllegalStateException.

## 6. Multithreading & Concurrency

### 1. Compare a process and a thread.
**Short:** A process is an independent program with its own memory; a thread is a lightweight unit inside a process sharing its memory.

**Brief:** Processes have separate address spaces, so communication between them is expensive (IPC). Threads within a process share heap and resources but each has its own stack and program counter, so they are cheaper to create and switch, but shared data needs synchronization.

### 2. What are the ways to create a thread? Compare Runnable and Callable.
**Short:** Extend Thread, implement Runnable or Callable, or use ExecutorService. Callable returns a value and can throw checked exceptions.

**Brief:** Implementing Runnable is preferred over extending Thread because Java has single inheritance and it separates the task from the execution mechanism. Runnable's `run()` returns void and cannot throw checked exceptions; Callable's `call()` returns a result and can throw exceptions, and it is submitted to an ExecutorService, which returns a Future. In real projects we always use thread pools rather than creating threads manually.

### 3. What is the thread lifecycle and its states?
**Short:** NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING and TERMINATED.

**Brief:** A thread is NEW after creation, becomes RUNNABLE after `start()`, goes BLOCKED while waiting for a monitor lock, WAITING on `wait()`/`join()` without timeout, TIMED_WAITING on `sleep(ms)` or `wait(ms)`, and TERMINATED once `run()` completes. Thread dumps (jstack) show these states and help debug deadlocks and hangs.

### 4. Compare `start()` and `run()`.
**Short:** start() creates a new thread that calls run(); calling run() directly executes in the current thread.

**Brief:** `start()` asks the JVM to create a new call stack and then invokes `run()` in it. Calling `run()` directly is just a normal method call with no new thread. Calling `start()` twice throws IllegalThreadStateException.

### 5. Compare `sleep()` and `wait()`, and `notify()` and `notifyAll()`.
**Short:** sleep pauses without releasing the lock; wait releases the lock until notified. notify wakes one waiting thread, notifyAll wakes all.

**Brief:** `Thread.sleep()` is a static Thread method usable anywhere and keeps any locks held. `wait()` is an Object method, must be called inside synchronized on the same object, releases the monitor, and should be called in a while loop to handle spurious wakeups. `notify()` wakes one arbitrary waiter; `notifyAll()` wakes all of them, which is safer when threads wait on different conditions.

### 6. Compare the synchronized method and block. What is a class-level vs. object-level lock?
**Short:** A synchronized block locks only the critical section on a chosen object; object locks are per instance, class locks are per Class.

**Brief:** A synchronized instance method locks `this`; a synchronized block can lock a specific object and keep the critical section small, improving performance. A static synchronized method (or `synchronized(MyClass.class)`) takes the class-level lock, shared by all instances, protecting static data. Two threads on different instances can enter an object-locked method at the same time, but not a class-locked one.

### 7. What is `volatile`? Compare it with `synchronized`.
**Short:** volatile guarantees visibility of changes across threads; synchronized guarantees both visibility and atomicity.

**Brief:** A volatile variable is always read from and written to main memory, and it prevents instruction reordering, so a change by one thread is immediately visible to others — typical for status flags like `running = false`. It does not make compound operations like `count++` atomic; for that you need synchronized, locks or AtomicInteger. volatile is also required for the double-checked locking Singleton.

### 8. What are deadlock, livelock, and starvation? How do you prevent a deadlock?
**Short:** Deadlock: threads wait for each other forever. Livelock: threads keep reacting but make no progress. Starvation: a thread never gets resources.

**Brief:** A deadlock happens when thread 1 holds lock A and waits for B, while thread 2 holds B and waits for A. Prevent it by always acquiring locks in a consistent order, using `tryLock` with a timeout, minimising nested locks and keeping locked sections short. Detect it with a thread dump (jstack shows "Found one Java-level deadlock"). Fair locks help against starvation.

### 9. What are race conditions and thread safety?
**Short:** A race condition is when the result depends on thread timing; thread-safe code behaves correctly under concurrent access.

**Brief:** For example, two threads doing `count++` (read, increment, write) can lose updates. Achieve thread safety with immutability, confinement (local variables, ThreadLocal), synchronization or locks, atomic classes and concurrent collections. Spring singleton beans are shared across request threads, so they must be stateless — no mutable instance fields.

### 10. Explain the Executor Framework and the types of thread pools.
**Short:** It separates task submission from execution using managed thread pools.

**Brief:** ExecutorService manages a pool of reusable threads and a task queue. `Executors` provides newFixedThreadPool (fixed threads, unbounded queue), newCachedThreadPool (creates threads as needed, risky under load), newSingleThreadExecutor (sequential execution), newScheduledThreadPool (delayed or periodic tasks) and, in Java 21, newVirtualThreadPerTaskExecutor. In production I create a ThreadPoolExecutor with a bounded queue, or use Spring's ThreadPoolTaskExecutor with `@Async`, so load cannot cause OOM.

### 11. Compare `submit()` and `execute()`, and `shutdown()` and `shutdownNow()`.
**Short:** execute runs a Runnable with no result; submit returns a Future. shutdown is graceful; shutdownNow interrupts running tasks.

**Brief:** `execute()` comes from Executor, and uncaught exceptions go to the thread's handler. `submit()` accepts Runnable or Callable and returns a Future; exceptions are captured and rethrown wrapped in ExecutionException when you call `get()`. `shutdown()` stops accepting new tasks and lets queued tasks finish; `shutdownNow()` interrupts running tasks and returns pending ones. Use `awaitTermination()` to wait for completion.

### 12. Explain Future and CompletableFuture, and chaining.
**Short:** Future is a blocking result handle; CompletableFuture supports non-blocking chaining, combining and error handling.

**Brief:** `Future.get()` blocks and cannot be chained or completed manually. CompletableFuture lets you run async work with `supplyAsync()` and pipe results: `thenApply` transforms a result (like map), `thenCompose` chains another async call (like flatMap), `thenCombine` merges two independent futures, `allOf` waits for many, and `exceptionally`/`handle` deal with errors. A real use: calling patient, insurance and appointment services in parallel and combining results, cutting response time from the sum to the maximum of the three. Pass a custom executor instead of relying on the common pool.

### 13. Compare ReentrantLock and synchronized. What are ReadWriteLock and tryLock?
**Short:** ReentrantLock is an explicit lock with extra features; ReadWriteLock allows many readers or one writer.

**Brief:** Both are reentrant, but ReentrantLock adds `tryLock()` with timeout (avoids deadlock), `lockInterruptibly()`, fairness and multiple Condition objects. You must release it in finally: `lock.lock(); try { ... } finally { lock.unlock(); }`. synchronized is simpler and released automatically. ReentrantReadWriteLock lets multiple threads read simultaneously while writes are exclusive, which suits read-heavy caches.

### 14. What are atomic classes, and what is CAS?
**Short:** Atomic classes provide lock-free thread-safe operations using Compare-And-Swap.

**Brief:** AtomicInteger, AtomicLong and AtomicReference offer methods like `incrementAndGet()` and `compareAndSet(expected, new)`. CAS is a CPU instruction that updates a value only if it still equals the expected value; otherwise it retries. This avoids locking overhead for counters. LongAdder performs better under very high contention.

### 15. What are CountDownLatch, CyclicBarrier, and Semaphore?
**Short:** CountDownLatch waits for N events once; CyclicBarrier makes N threads wait for each other and is reusable; Semaphore limits concurrent access to N permits.

**Brief:** With CountDownLatch, the main thread calls `await()` until workers call `countDown()` N times — e.g. waiting for services to initialise; it cannot be reset. CyclicBarrier makes threads wait at a common point until all arrive, then optionally runs an action, and can be reused. Semaphore controls how many threads can access a resource, e.g. allowing only 5 concurrent calls to a rate-limited external API.

### 16. What is ThreadLocal, and how is it used in web apps?
**Short:** It gives each thread its own copy of a variable.

**Brief:** Spring uses ThreadLocal to store the SecurityContext, transaction context and request attributes per request thread. We use it via MDC to keep a correlation ID for logging. Because servers use thread pools, you must call `remove()` in a finally block or filter, otherwise data leaks to the next request and memory can leak.

### 17. What are daemon threads?
**Short:** Background threads that do not prevent the JVM from exiting.

**Brief:** The JVM exits when only daemon threads remain; garbage collection is an example. Mark a thread with `setDaemon(true)` before `start()`. Do not use daemon threads for work that must complete, like writing files, because they can be stopped abruptly.

### 18. Explain the producer-consumer problem.
**Short:** Producers add items to a shared buffer and consumers remove them; coordinate with wait/notify or a BlockingQueue.

**Brief:** With wait/notify, the producer waits while the buffer is full and the consumer waits while it is empty, each notifying the other in synchronized blocks. The simpler and preferred way is a BlockingQueue:

```java
BlockingQueue<Integer> q = new ArrayBlockingQueue<>(10);
// producer
q.put(item);          // blocks when full
// consumer
Integer x = q.take(); // blocks when empty
```

### 19. What is the Java Memory Model, and what is happens-before?
**Short:** The JMM defines how threads see shared memory; happens-before guarantees visibility and ordering between actions.

**Brief:** Threads may cache variables and the compiler or CPU can reorder instructions, so without rules one thread might never see another's write. Happens-before relationships guarantee visibility: an unlock happens-before a later lock of the same monitor, a volatile write happens-before subsequent reads, `Thread.start()` happens-before actions in the new thread, and thread actions happen-before another thread's successful `join()`.

### 20. What are the thread pool parameters?
**Short:** corePoolSize, maximumPoolSize, keepAliveTime, workQueue, threadFactory and rejection handler.

**Brief:** Tasks first use core threads; when all are busy, tasks go into the queue; only when the queue is full are extra threads created up to max; beyond that the rejection policy applies: AbortPolicy (throws, the default), CallerRunsPolicy (caller runs it — natural backpressure), DiscardPolicy or DiscardOldestPolicy. Sizing rule of thumb: about CPU cores + 1 for CPU-bound work, more for I/O-bound work. Name threads via a ThreadFactory for easier debugging.

### 21. Write a program that prints odd and even numbers using two threads.
**Short:** Use a shared lock and wait/notify so the threads alternate.

**Brief:**
```java
class OddEven {
    private int n = 1; private final int max = 10;
    synchronized void print(boolean odd) throws InterruptedException {
        while (n <= max) {
            while ((n % 2 == 1) != odd) { wait(); if (n > max) return; }
            System.out.println(Thread.currentThread().getName() + ": " + n++);
            notifyAll();
        }
    }
    public static void main(String[] a) {
        OddEven p = new OddEven();
        new Thread(() -> { try { p.print(true); } catch (InterruptedException e) {} }, "Odd").start();
        new Thread(() -> { try { p.print(false); } catch (InterruptedException e) {} }, "Even").start();
    }
}
```

### 22. Where have you used multithreading in your project?
**Short:** Parallel downstream calls with CompletableFuture, `@Async` notifications and batch processing with a thread pool.

**Brief:** Example answer to adapt: "In our application the dashboard needed data from three services. Calls were sequential and took about 1.5 seconds; I used CompletableFuture with a custom ThreadPoolTaskExecutor to call them in parallel and combine results, reducing it to around 500 ms. We also used `@Async` for sending email notifications so the API returned immediately, and an ExecutorService to process a nightly file of records in chunks. I made sure to handle exceptions with `exceptionally`, set timeouts, and propagate the MDC correlation ID to async threads." Use real numbers from your own work.

## 7. Design Patterns & DSA

### 1. Write a thread-safe Singleton. How can a Singleton be broken?
**Short:** Use double-checked locking with volatile, or an enum; reflection, serialization and cloning can break it.

**Brief:**
```java
public class Config {
    private static volatile Config instance;
    private Config() { if (instance != null) throw new IllegalStateException(); }
    public static Config getInstance() {
        if (instance == null) {
            synchronized (Config.class) {
                if (instance == null) instance = new Config();
            }
        }
        return instance;
    }
    protected Object readResolve() { return getInstance(); } // protects serialization
}
```
Reflection can call the private constructor (guard by throwing in the constructor), deserialization creates a new object (fix with `readResolve`), and cloning creates a copy (override clone to throw). An enum Singleton is safe against all three. In Spring, beans are singletons per container by default.

### 2. Explain Factory vs. Abstract Factory.
**Short:** Factory creates one type of object based on input; Abstract Factory creates families of related objects.

**Brief:** A Factory method, like `NotificationFactory.create("EMAIL")`, returns the right implementation of one interface, hiding `new` from the client. An Abstract Factory is a factory of factories producing related objects that must be used together, like a UIFactory producing matching Button and Checkbox for a Windows or Mac theme.

### 3. Explain Builder, Strategy, Observer, Adapter, Decorator, Proxy, and Template patterns.
**Short:** Builder builds complex objects step by step; Strategy swaps algorithms; Observer notifies subscribers; Adapter converts interfaces; Decorator adds behaviour; Proxy controls access; Template defines an algorithm skeleton.

**Brief:** Builder (Lombok `@Builder`) avoids telescoping constructors. Strategy picks an algorithm at runtime, e.g. different payment or discount strategies injected as a `Map<String, Strategy>`. Observer is publish-subscribe, like Spring ApplicationEvents or Kafka consumers. Adapter wraps a legacy or third-party API to fit our interface. Decorator wraps an object to add behaviour, like BufferedReader around FileReader. Proxy adds cross-cutting behaviour, like Spring's `@Transactional` proxies. Template Method fixes the steps and lets subclasses fill in parts, like JdbcTemplate.

### 4. Which design patterns does Spring use internally?
**Short:** Singleton, Factory, Proxy, Template, Observer, Front Controller and Dependency Injection.

**Brief:** Beans are singletons by default; BeanFactory and ApplicationContext are factories; AOP, `@Transactional` and `@Cacheable` work through JDK or CGLIB proxies; JdbcTemplate, RestTemplate and KafkaTemplate follow the Template pattern; ApplicationEventPublisher implements Observer; DispatcherServlet is a Front Controller; and HandlerAdapter is an Adapter.

### 5. What microservice patterns do you know?
**Short:** API Gateway, Service Discovery, Circuit Breaker, Saga, CQRS, Database per Service and Event Sourcing.

**Brief:** An API Gateway provides a single entry point for routing, authentication and rate limiting. Circuit Breaker stops calling a failing service and returns a fallback. Saga manages distributed transactions with local transactions and compensating actions. CQRS separates write and read models for scaling and performance. Others include Strangler Fig for migrating monoliths, Bulkhead for isolating resources, and the Outbox pattern for reliable event publishing.

### Coding (DSA) Questions

### 1. Reverse a string or check for a palindrome.
**Short:** Use StringBuilder.reverse(), or two pointers.

**Brief:**
```java
String rev = new StringBuilder(s).reverse().toString();
boolean isPalindrome(String s) {
    int i = 0, j = s.length() - 1;
    while (i < j) if (s.charAt(i++) != s.charAt(j--)) return false;
    return true;
}
```

### 2. Check if two strings are anagrams.
**Short:** Compare sorted char arrays, or count characters.

**Brief:**
```java
boolean isAnagram(String a, String b) {
    if (a.length() != b.length()) return false;
    int[] count = new int[26];
    for (int i = 0; i < a.length(); i++) { count[a.charAt(i) - 'a']++; count[b.charAt(i) - 'a']--; }
    for (int c : count) if (c != 0) return false;
    return true;
}
```
This is O(n); sorting would be O(n log n).

### 3. Find the missing number in an array.
**Short:** Expected sum n(n+1)/2 minus actual sum (or XOR).

**Brief:**
```java
int missing(int[] arr, int n) { // numbers 1..n with one missing
    int sum = n * (n + 1) / 2;
    for (int x : arr) sum -= x;
    return sum;
}
```
Use long or XOR to avoid overflow for large n.

### 4. Solve the two-sum problem.
**Short:** Use a HashMap of value to index in one pass — O(n).

**Brief:**
```java
int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        Integer j = map.get(target - nums[i]);
        if (j != null) return new int[]{j, i};
        map.put(nums[i], i);
    }
    return new int[0];
}
```

### 5. Find the longest substring without repeating characters.
**Short:** Sliding window with a map of last-seen positions — O(n).

**Brief:**
```java
int longest(String s) {
    Map<Character, Integer> last = new HashMap<>();
    int best = 0, start = 0;
    for (int i = 0; i < s.length(); i++) {
        char c = s.charAt(i);
        if (last.containsKey(c) && last.get(c) >= start) start = last.get(c) + 1;
        last.put(c, i);
        best = Math.max(best, i - start + 1);
    }
    return best;
}
```

### 6. Reverse a linked list and detect a cycle.
**Short:** Reverse with three pointers; detect a cycle with Floyd's slow/fast pointers.

**Brief:**
```java
Node reverse(Node head) {
    Node prev = null, cur = head;
    while (cur != null) { Node next = cur.next; cur.next = prev; prev = cur; cur = next; }
    return prev;
}
boolean hasCycle(Node head) {
    Node slow = head, fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next; fast = fast.next.next;
        if (slow == fast) return true;
    }
    return false;
}
```

### 7. Validate balanced parentheses using a stack.
**Short:** Push opening brackets, pop and match on closing ones; the stack must be empty at the end.

**Brief:**
```java
boolean isValid(String s) {
    Deque<Character> st = new ArrayDeque<>();
    for (char c : s.toCharArray()) {
        if (c == '(') st.push(')'); else if (c == '[') st.push(']'); else if (c == '{') st.push('}');
        else if (st.isEmpty() || st.pop() != c) return false;
    }
    return st.isEmpty();
}
```

### 8. Merge two sorted arrays.
**Short:** Two pointers, always taking the smaller element — O(n + m).

**Brief:**
```java
int[] merge(int[] a, int[] b) {
    int[] r = new int[a.length + b.length];
    int i = 0, j = 0, k = 0;
    while (i < a.length && j < b.length) r[k++] = a[i] <= b[j] ? a[i++] : b[j++];
    while (i < a.length) r[k++] = a[i++];
    while (j < b.length) r[k++] = b[j++];
    return r;
}
```

### 9. Find the largest and second-largest elements without sorting.
**Short:** Single pass keeping two variables.

**Brief:**
```java
int first = Integer.MIN_VALUE, second = Integer.MIN_VALUE;
for (int x : arr) {
    if (x > first) { second = first; first = x; }
    else if (x > second && x != first) second = x;
}
```

### 10. Implement an LRU cache using LinkedHashMap.
**Short:** Use access-order LinkedHashMap and override removeEldestEntry.

**Brief:**
```java
class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int capacity;
    LRUCache(int capacity) { super(capacity, 0.75f, true); this.capacity = capacity; }
    @Override protected boolean removeEldestEntry(Map.Entry<K, V> eldest) { return size() > capacity; }
}
```
Both get and put are O(1). The manual version uses a HashMap plus a doubly linked list.

### 11. Explain Big-O for common collection operations.
**Short:** ArrayList get O(1), HashMap get/put O(1) average, TreeMap O(log n), LinkedList index access O(n).

**Brief:** ArrayList: get O(1), add at end amortised O(1), insert/remove in the middle O(n), contains O(n). LinkedList: add/remove at ends O(1), get(i) O(n). HashMap/HashSet: O(1) average, O(log n) worst case since Java 8. TreeMap/TreeSet: O(log n). PriorityQueue: offer/poll O(log n), peek O(1). Sorting with `Collections.sort` is O(n log n) (TimSort).
## 8. Spring & Spring Boot

### 1. Compare Spring and Spring Boot. What are Spring Boot's advantages?
**Short:** Spring is the framework; Spring Boot is an opinionated layer that removes setup through auto-configuration and starters.

**Brief:** Plain Spring needs manual configuration of beans, the DispatcherServlet, a data source and an external server. Spring Boot provides auto-configuration based on the classpath, starter dependencies with compatible versions, an embedded Tomcat so the app runs as a jar, externalised configuration with profiles, and production features via Actuator. This makes it ideal for microservices.

### 2. What are IoC and Dependency Injection? Which injection type is preferred?
**Short:** IoC means the container creates and wires objects; DI is how it supplies dependencies. Constructor injection is preferred.

**Brief:** Instead of a class creating its dependencies with `new`, the Spring container creates beans and injects them, giving loose coupling and easy testing with mocks. Constructor injection is preferred because dependencies can be final (immutable), required dependencies are guaranteed, it is easy to test without Spring, and circular dependencies fail fast. Setter injection suits optional dependencies; field injection with `@Autowired` hides dependencies and is harder to test. With a single constructor, `@Autowired` is not even needed — I use Lombok `@RequiredArgsConstructor`.

### 3. What does `@SpringBootApplication` contain internally?
**Short:** `@SpringBootConfiguration` + `@EnableAutoConfiguration` + `@ComponentScan`.

**Brief:** `@SpringBootConfiguration` is a specialised `@Configuration` marking the class as a source of bean definitions. `@EnableAutoConfiguration` loads auto-configuration classes based on the classpath and properties. `@ComponentScan` scans the main class's package and sub-packages for components — which is why the main class should be in the root package.

### 4. How does auto-configuration work?
**Short:** Spring Boot loads configuration classes listed in its metadata and applies them conditionally.

**Brief:** Boot reads the list of auto-configuration classes from `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` (spring.factories in older versions). Each class uses conditions like `@ConditionalOnClass`, `@ConditionalOnMissingBean` and `@ConditionalOnProperty` — for example, a DataSource is configured only if a JDBC driver is on the classpath and you have not defined your own. Run with `--debug` to see the conditions evaluation report.

### 5. What are bean scopes?
**Short:** singleton (default), prototype, request, session, application and websocket.

**Brief:** Singleton creates one instance per container. Prototype creates a new instance each time it is requested, and Spring does not manage its destruction. Request, session and application scopes exist only in web apps. Injecting a prototype into a singleton gives only one instance unless you use `ObjectProvider`, `@Lookup` or a scoped proxy.

### 6. What is the bean lifecycle?
**Short:** Instantiate → inject dependencies → aware callbacks → BeanPostProcessor → init → use → destroy.

**Brief:** The container creates the bean, injects properties, calls Aware interfaces (like BeanNameAware), runs BeanPostProcessor `postProcessBeforeInitialization`, calls `@PostConstruct` or `afterPropertiesSet` or a custom init-method, then `postProcessAfterInitialization` (where AOP proxies are created). On shutdown it calls `@PreDestroy` or `destroy()`. I use `@PostConstruct` for warming caches or validating configuration.

### 7. Compare `@Component`, `@Service`, `@Repository`, and `@Controller`.
**Short:** All are stereotype annotations for component scanning; the specialised ones add meaning and some behaviour.

**Brief:** `@Component` is the generic one. `@Service` marks business logic and is purely semantic. `@Repository` marks the data access layer and enables translation of persistence exceptions into Spring's DataAccessException. `@Controller` marks a web MVC controller handled by the DispatcherServlet.

### 8. Compare `@Controller` and `@RestController`.
**Short:** `@RestController` = `@Controller` + `@ResponseBody`.

**Brief:** `@Controller` methods usually return view names resolved by a ViewResolver (JSP, Thymeleaf). `@RestController` writes return values directly to the HTTP response body, converted to JSON by Jackson's HttpMessageConverter — used for REST APIs consumed by React.

### 9. Compare `@Autowired`, `@Qualifier`, and `@Primary`.
**Short:** @Autowired injects by type; @Qualifier picks a specific bean by name; @Primary marks the default when multiple beans match.

**Brief:** With two implementations of PaymentService, autowiring by type throws NoUniqueBeanDefinitionException. Mark one `@Primary` to make it the default, or use `@Qualifier("upiPayment")` at the injection point to choose explicitly; @Qualifier takes precedence over @Primary.

### 10. What are `@Value`, `@ConfigurationProperties`, profiles, and `application.yml`?
**Short:** @Value injects single properties; @ConfigurationProperties binds groups of properties to a class; profiles provide environment-specific configuration.

**Brief:** `@Value("${app.timeout:5000}")` injects one value with a default. `@ConfigurationProperties(prefix = "app.mail")` binds related properties to a type-safe, validatable POJO or record — better for many properties. Profiles like `application-dev.yml` and `application-prod.yml` are activated with `spring.profiles.active`, and `@Profile("dev")` limits beans to a profile. YAML supports hierarchy; secrets come from environment variables or a vault, never from the repository.

### 11. What are Spring Boot starters, and what is Actuator?
**Short:** Starters are dependency bundles; Actuator exposes health, metrics and management endpoints.

**Brief:** `spring-boot-starter-web` brings Spring MVC, Jackson and embedded Tomcat with tested versions; others include data-jpa, security, test and validation. Actuator provides `/actuator/health` (used by Kubernetes liveness and readiness probes), `/metrics`, `/info`, `/env` and `/loggers`, and integrates with Prometheus via Micrometer. Expose only the needed endpoints and secure them.

### 12. How do you handle circular dependencies?
**Short:** Redesign to break the cycle; as a workaround, use setter injection or `@Lazy`.

**Brief:** Spring Boot 2.6+ fails at startup on circular references by default. The right fix is refactoring: extract the shared logic into a third bean, or use events to decouple. Temporary workarounds are `@Lazy` on one injection point (which injects a proxy) or setter injection, but a cycle usually signals a design problem.

### 13. What is global exception handling?
**Short:** `@RestControllerAdvice` with `@ExceptionHandler` methods handles exceptions from all controllers in one place.

**Brief:**
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(PatientNotFoundException.class)
    public ResponseEntity<ErrorResponse> notFound(PatientNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
            .body(new ErrorResponse("NOT_FOUND", ex.getMessage(), Instant.now()));
    }
    @ExceptionHandler(MethodArgumentNotValidException.class)
    // 400 with field errors
    public ResponseEntity<ErrorResponse> invalid(MethodArgumentNotValidException ex) { ... }
    @ExceptionHandler(Exception.class)
    // log, return 500 without internals
    public ResponseEntity<ErrorResponse> generic(Exception ex) { ... }
}
```
This gives a consistent error format; Spring 6 also supports the standard ProblemDetail (RFC 7807).

### 14. How do you do request validation?
**Short:** Bean Validation annotations on DTOs plus `@Valid` on the controller parameter.

**Brief:** Annotate DTO fields with `@NotNull`, `@NotBlank`, `@Email`, `@Size`, `@Pattern`, `@Past` and so on, and put `@Valid @RequestBody PatientRequest req` in the controller. Failures throw MethodArgumentNotValidException, which the advice converts to a 400 with field errors. Use `@Validated` on the class for path and query parameter validation, validation groups for create vs. update, and custom validators via a `@Constraint` annotation plus a ConstraintValidator.

### 15. Explain `@Transactional` propagation and isolation. Why does it not work on self-invocation?
**Short:** Propagation defines how transactions join or start; isolation defines visibility between transactions. Self-invocation bypasses the proxy.

**Brief:** Propagation: REQUIRED (default — join or create), REQUIRES_NEW (suspend and start a new one, e.g. audit logging that must commit even if the main transaction fails), NESTED, SUPPORTS, MANDATORY, NOT_SUPPORTED and NEVER. Isolation: READ_UNCOMMITTED, READ_COMMITTED, REPEATABLE_READ and SERIALIZABLE (DEFAULT uses the database's). By default rollback happens only for unchecked exceptions; use `rollbackFor = Exception.class` for checked ones. `@Transactional` works through a proxy, so calling a transactional method from another method of the same class (`this.method()`) skips the proxy and no transaction starts; private methods also do not work. Fix by moving the method to another bean.

### 16. What is AOP? Give logging as an example.
**Short:** Aspect-Oriented Programming separates cross-cutting concerns like logging, security and transactions from business logic.

**Brief:** An aspect is a class holding cross-cutting logic; advice is the action (@Before, @After, @AfterReturning, @AfterThrowing, @Around); a pointcut is an expression selecting where to apply it; a join point is a method execution. For example:

```java
@Aspect @Component
public class LoggingAspect {
    @Around("execution(* com.kp.service..*(..))")
    public Object time(ProceedingJoinPoint pjp) throws Throwable {
        long start = System.currentTimeMillis();
        try { return pjp.proceed(); }
        finally { log.info("{} took {} ms", pjp.getSignature(), System.currentTimeMillis() - start); }
    }
}
```

### 17. Explain the DispatcherServlet flow in Spring MVC.
**Short:** Request → DispatcherServlet → HandlerMapping → HandlerAdapter → Controller → response via MessageConverter or ViewResolver.

**Brief:** The DispatcherServlet (front controller) receives every request, passes through filters, asks HandlerMapping which controller method matches, runs interceptors' `preHandle`, and invokes the method via a HandlerAdapter, which resolves arguments (@RequestBody, @PathVariable) and runs validation. The return value is converted to JSON by HttpMessageConverter (or resolved to a view), interceptors' `postHandle`/`afterCompletion` run, and exceptions go to HandlerExceptionResolvers such as `@ControllerAdvice`.

### 18. Compare filters and interceptors.
**Short:** Filters are servlet-level and run before the DispatcherServlet; interceptors are Spring MVC-level and run around controllers.

**Brief:** Filters (jakarta.servlet.Filter or OncePerRequestFilter) see every request, including static resources, and suit authentication (Spring Security is a filter chain), CORS, logging and adding correlation IDs. Interceptors (HandlerInterceptor) know which handler is executing and have `preHandle`, `postHandle` and `afterCompletion`, which suits auditing, locale and handler-specific checks.

### 19. How do you change the default port or exclude an auto-configuration?
**Short:** Set `server.port`; use `exclude` in `@SpringBootApplication`.

**Brief:** `server.port=8081` in application.yml, an environment variable `SERVER_PORT`, or `--server.port=8081` on the command line (0 gives a random port). Exclude with `@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)` or the `spring.autoconfigure.exclude` property.

### 20. Compare `CommandLineRunner` and `ApplicationRunner`.
**Short:** Both run code once after startup; CommandLineRunner gets raw String args, ApplicationRunner gets parsed ApplicationArguments.

**Brief:** They are used for tasks like seeding data, warming caches or checks at startup. ApplicationArguments gives easy access to option arguments like `--mode=test`. Multiple runners can be ordered with `@Order`.

## 9. REST APIs

### 1. What are REST principles? Compare REST and SOAP.
**Short:** REST is a resource-based, stateless architecture over HTTP; SOAP is a strict XML protocol.

**Brief:** REST principles are client-server separation, statelessness (each request carries everything needed, such as a JWT), cacheability, a uniform interface (resources identified by URIs, manipulated with HTTP verbs), a layered system and optional code on demand. REST is lightweight, supports JSON and suits web and mobile. SOAP uses XML envelopes with a WSDL contract and WS-Security, still found in some enterprise and healthcare legacy integrations.

### 2. Compare the HTTP methods. Which are idempotent? Compare PUT and PATCH.
**Short:** GET reads, POST creates, PUT replaces, PATCH partially updates, DELETE removes; GET, PUT and DELETE are idempotent, POST is not.

**Brief:** Idempotent means repeating the request gives the same server state. GET, HEAD and OPTIONS are also safe (no changes). POST twice creates two resources, so we use idempotency keys for payments. PUT sends the full representation and replaces it; PATCH sends only the changed fields, for example updating only a patient's phone number. PATCH is not guaranteed to be idempotent.

### 3. What are the important status codes?
**Short:** 2xx success, 4xx client error, 5xx server error.

**Brief:** 200 OK (successful GET/PUT), 201 Created (POST, with a Location header), 204 No Content (DELETE), 400 Bad Request (validation failure), 401 Unauthorized (not authenticated or invalid token), 403 Forbidden (authenticated but no permission), 404 Not Found, 409 Conflict (duplicate or version conflict), 422 Unprocessable Entity, 429 Too Many Requests, 500 Internal Server Error, 502/503/504 for gateway, unavailable and timeout problems.

### 4. Compare `@PathVariable` and `@RequestParam`, and `@RequestBody` and `ResponseEntity`.
**Short:** PathVariable identifies a resource from the URI path; RequestParam reads query parameters; RequestBody maps JSON to an object; ResponseEntity controls the full response.

**Brief:** `/patients/{id}` uses `@PathVariable Long id`; `/patients?status=ACTIVE&page=0` uses `@RequestParam` for filtering and paging, optionally with `required=false` or a default value. `@RequestBody` deserialises the JSON body into a DTO. `ResponseEntity<T>` lets you set the status, headers and body, e.g. `ResponseEntity.created(uri).body(dto)`.

### 5. What are the strategies for API versioning?
**Short:** URI versioning, request parameter, custom header, or media type (content negotiation).

**Brief:** URI versioning (`/api/v1/patients`) is the most common and easiest to route and cache. Others are `?version=1`, a header like `X-API-VERSION: 1`, and `Accept: application/vnd.kp.v1+json`. Keep old versions running until clients migrate, and avoid breaking changes within a version.

### 6. How do you implement pagination and sorting?
**Short:** Accept a Pageable in the controller and return a Page from Spring Data.

**Brief:** `GET /patients?page=0&size=20&sort=lastName,asc` maps to a `Pageable` parameter; `repository.findAll(pageable)` returns a `Page<T>` with content, totalElements and totalPages. Limit the maximum page size. For very large datasets, keyset (cursor) pagination performs better than offset because OFFSET scans skipped rows.

### 7. How do you secure a REST API?
**Short:** HTTPS, authentication with JWT/OAuth2, role-based authorization, input validation and rate limiting.

**Brief:** Use TLS everywhere; authenticate with OAuth 2.0 / OpenID Connect and JWT validated by Spring Security; authorise with roles or scopes using `@PreAuthorize`; validate and sanitise all input; use parameterised queries; configure CORS strictly; rate-limit at the gateway; never log tokens or PHI; return generic error messages; and keep dependencies patched. In healthcare, HIPAA compliance adds audit logging of data access.

### 8. What is CORS, and how do you handle it in Spring Boot?
**Short:** CORS is a browser mechanism that controls cross-origin requests; configure allowed origins on the server.

**Brief:** When React on `localhost:3000` calls an API on `localhost:8080`, the browser sends a preflight OPTIONS request, and the server must answer with Access-Control-Allow-Origin and related headers. Configure it globally through a `WebMvcConfigurer.addCorsMappings` or a CorsConfigurationSource in Spring Security, or with `@CrossOrigin` on a controller. Never use `*` with credentials in production; list exact origins.

### 9. Compare RestTemplate, WebClient, and Feign Client.
**Short:** RestTemplate is synchronous and in maintenance mode; WebClient is reactive and non-blocking; Feign is a declarative interface-based client.

**Brief:** RestTemplate is simple and blocking. WebClient (WebFlux) supports async and streaming calls and can also be used in blocking style. OpenFeign lets you declare an interface with `@FeignClient(name = "patient-service")` and Spring generates the implementation, integrating with service discovery, load balancing and Resilience4j — very common in microservices. Spring 6.1 also adds RestClient, the modern synchronous replacement for RestTemplate.

### 10. How do you document APIs?
**Short:** Swagger/OpenAPI using springdoc-openapi.

**Brief:** Adding the springdoc-openapi dependency auto-generates an OpenAPI spec at `/v3/api-docs` and Swagger UI at `/swagger-ui.html` from controllers. Enrich it with `@Operation`, `@ApiResponse` and `@Schema`. Teams also use the spec for contract-first development and client generation, and test APIs with Postman collections.

## 10. JPA / Hibernate

### 1. Compare JPA, Hibernate, and Spring Data JPA.
**Short:** JPA is the specification, Hibernate is an implementation, and Spring Data JPA is an abstraction that generates repositories on top.

**Brief:** JPA (jakarta.persistence) defines annotations and the EntityManager API. Hibernate implements JPA and adds features like caching and batch fetching. Spring Data JPA removes boilerplate: you declare an interface extending JpaRepository and Spring generates the implementation, including derived queries, paging and auditing.

### 2. Compare CrudRepository, JpaRepository, and PagingAndSortingRepository.
**Short:** CrudRepository gives basic CRUD; PagingAndSortingRepository adds paging and sorting; JpaRepository adds JPA-specific features.

**Brief:** CrudRepository has save, findById, findAll, delete and count. PagingAndSortingRepository adds `findAll(Pageable)` and `findAll(Sort)`. JpaRepository extends both and adds flush, `saveAndFlush`, batch deletes and `getReferenceById`, and returns Lists instead of Iterables. I usually extend JpaRepository.

### 3. What are entity states?
**Short:** Transient (new), Persistent (managed), Detached and Removed.

**Brief:** A new object not associated with a persistence context is transient. After `persist()` or loading, it is managed: changes are tracked and synced at flush or commit. When the persistence context closes or `detach()` is called, it becomes detached and changes are not tracked; `merge()` reattaches a copy. `remove()` marks it for deletion on flush.

### 4. Compare lazy and eager loading. What is LazyInitializationException?
**Short:** Lazy loads associations on first access; eager loads them immediately. LazyInitializationException occurs when accessing a lazy association after the session closes.

**Brief:** Defaults: `@OneToMany` and `@ManyToMany` are LAZY; `@ManyToOne` and `@OneToOne` are EAGER — I usually make all of them LAZY. Accessing a lazy collection outside the transaction (e.g. during JSON serialization in the controller) throws LazyInitializationException. Fix it by fetching what you need in the service layer with a JOIN FETCH or `@EntityGraph`, and mapping to DTOs, rather than enabling open-in-view or making everything EAGER.

### 5. What is the N+1 problem, and how do you fix it?
**Short:** One query loads N parents, then N more queries load each child collection. Fix it with fetch join, @EntityGraph or batch fetching.

**Brief:** For example, loading 100 departments and then accessing each one's employees triggers 1 + 100 queries, killing performance. Fixes: `@Query("select d from Department d join fetch d.employees")`, `@EntityGraph(attributePaths = "employees")` on the repository method, `@BatchSize` or `hibernate.default_batch_fetch_size` to load children in IN batches, or DTO projections. I detect it by enabling SQL logging or Hibernate statistics in development.

### 6. What are the relationship mappings, `mappedBy`, and cascade types?
**Short:** `@OneToOne`, `@OneToMany`, `@ManyToOne` and `@ManyToMany`; mappedBy marks the inverse side; cascade propagates operations.

**Brief:** In a bidirectional relationship the owning side has the foreign key — usually the `@ManyToOne` side with `@JoinColumn` — and the other side uses `mappedBy` to avoid a duplicate join table. Cascade types (PERSIST, MERGE, REMOVE, REFRESH, DETACH, ALL) propagate operations from parent to child, e.g. saving an Order saves its OrderItems. `orphanRemoval = true` deletes children removed from the collection. Avoid cascade REMOVE on `@ManyToMany`; prefer a join entity for many-to-many with extra columns.

### 7. Explain first-level vs. second-level cache.
**Short:** First-level cache is per session and on by default; second-level cache is shared across sessions and optional.

**Brief:** Within one transaction or session, loading the same entity by id twice hits the database once — that is the first-level cache. The second-level cache is at the SessionFactory level, shared across sessions, enabled with a provider like Ehcache or Hazelcast and `@Cacheable`/`@Cache` on entities, which is good for read-mostly reference data. There is also a query cache. For distributed services we often use Redis with Spring's `@Cacheable` instead.

### 8. Compare `get()` and `load()`, and `save()`, `persist()`, and `merge()`.
**Short:** get hits the DB and returns null if not found; load returns a proxy and throws if not found. persist makes a new entity managed; merge copies a detached state; save is Hibernate's legacy method.

**Brief:** In JPA terms, `find()` behaves like get and `getReference()` (`getReferenceById` in Spring Data) behaves like load — useful for setting a foreign key without a SELECT. `persist()` returns void and works on new entities. `merge()` returns a managed copy and works for detached entities. Spring Data's `save()` calls persist if the entity is new (id null), otherwise merge.

### 9. What are derived query methods, `@Query`, JPQL, and native queries?
**Short:** Derived queries come from method names; `@Query` defines JPQL or native SQL explicitly.

**Brief:** `findByLastNameAndStatusOrderByCreatedAtDesc(String name, Status s)` is parsed into a query automatically. For complex queries use `@Query("select p from Patient p where p.dob < :date")` — JPQL works on entities and fields and is database-independent. `@Query(value = "...", nativeQuery = true)` uses real SQL for database-specific features. Updates need `@Modifying` with `@Transactional`. For dynamic filters use Specifications or Criteria API.

### 10. Compare optimistic and pessimistic locking.
**Short:** Optimistic uses a version check at commit; pessimistic locks the database row while reading.

**Brief:** With optimistic locking, `@Version` adds a version column; an update includes `where version = ?`, and if another transaction changed the row, OptimisticLockException is thrown so we retry or return 409. It suits low-contention cases like editing patient profiles. Pessimistic locking (`@Lock(LockModeType.PESSIMISTIC_WRITE)` → `SELECT ... FOR UPDATE`) blocks others, suiting high-contention cases like booking the last appointment slot, but risks deadlocks.

### 11. What are the ID generation strategies?
**Short:** AUTO, IDENTITY, SEQUENCE and TABLE (plus UUID).

**Brief:** IDENTITY uses database auto-increment (MySQL), but it disables JDBC insert batching because the id is known only after insert. SEQUENCE uses a database sequence (Oracle, PostgreSQL) and supports batching with allocationSize. TABLE simulates a sequence with a table and is slow. AUTO lets the provider choose. UUIDs are useful in distributed systems where ids must be unique across services.

### 12. What is dirty checking?
**Short:** Hibernate automatically detects changes to managed entities and issues UPDATEs at flush.

**Brief:** When an entity is loaded, Hibernate keeps a snapshot of it. At flush or commit it compares the current state to the snapshot and generates UPDATE statements for changed entities — so inside a `@Transactional` method you can just call setters without `save()`. For read-only operations, `@Transactional(readOnly = true)` skips dirty checking and improves performance.

## 11. Microservices

### 1. Compare monolithic and microservices architecture.
**Short:** A monolith is one deployable unit; microservices are small, independently deployable services around business capabilities.

**Brief:** Monoliths are simple to develop, test and deploy initially, but become hard to scale and change as they grow, and one bug can take everything down. Microservices allow independent deployment and scaling, technology choice per service, fault isolation and team autonomy. The costs are distributed-system complexity: network latency, data consistency, distributed tracing, more DevOps and harder testing. Microservices are not always the right choice for small teams.

### 2. How do you decide service boundaries?
**Short:** Use Domain-Driven Design — split by business capability or bounded context.

**Brief:** Each service should own one business capability and its data, like Patient, Appointment, Billing and Notification. Aim for high cohesion within a service and loose coupling between services; if two services always change together or chat constantly, they probably belong together. Consider team ownership and data ownership too.

### 3. Compare synchronous and asynchronous communication.
**Short:** Synchronous (REST/Feign) waits for a response; asynchronous (Kafka/RabbitMQ) sends events without waiting.

**Brief:** Synchronous REST is simple and good when you need an immediate answer, like validating a patient before booking, but it creates temporal coupling — if the downstream service is slow or down, the caller suffers. Asynchronous messaging decouples services, absorbs load spikes and improves resilience, ideal for notifications, audit and data sync, but brings eventual consistency, duplicate messages (consumers must be idempotent) and harder debugging.

### 4. What is service discovery?
**Short:** A registry where services register and look up each other's locations dynamically.

**Brief:** Instances scale up and down with changing IPs, so hard-coding URLs is impossible. With Eureka, each service registers on startup and sends heartbeats; clients look up instances by name and load-balance with Spring Cloud LoadBalancer — client-side discovery. In Kubernetes, the built-in Service and DNS provide server-side discovery, so Eureka is often unnecessary there.

### 5. What does an API Gateway do?
**Short:** It is the single entry point that routes requests and handles cross-cutting concerns.

**Brief:** Spring Cloud Gateway routes `/api/patients/**` to patient-service and so on, and centralises authentication and JWT validation, rate limiting, CORS, request logging, correlation IDs, SSL termination, response aggregation and circuit breakers. It hides internal service structure from clients like the React app.

### 6. What is centralized configuration?
**Short:** Spring Cloud Config Server serves configuration for all services from a central store like Git.

**Brief:** Services fetch configuration at startup by name and profile, so changes do not require rebuilding. With `@RefreshScope` and Actuator's refresh (or Spring Cloud Bus), properties can be reloaded at runtime. Secrets go into Vault or Kubernetes Secrets; in Kubernetes, ConfigMaps are a common alternative.

### 7. What are Circuit Breaker, Resilience4j, retry, fallback, and timeout?
**Short:** Resilience4j protects services from cascading failures using circuit breakers, retries, timeouts and fallbacks.

**Brief:** A circuit breaker monitors calls: when the failure rate crosses a threshold it OPENs and fails fast without calling the service, after a wait it goes HALF_OPEN to test a few calls, then CLOSEs if they succeed. A fallback returns a default or cached response. Retry handles transient failures with backoff (only for idempotent operations). A TimeLimiter or timeout prevents threads from waiting forever, and Bulkhead limits concurrent calls. Usage: `@CircuitBreaker(name = "insurance", fallbackMethod = "fallback")`.

### 8. How do you handle distributed transactions? Explain Saga.
**Short:** Avoid two-phase commit; use the Saga pattern — a sequence of local transactions with compensating actions.

**Brief:** Each service commits its own local transaction and publishes an event; if a later step fails, earlier steps are undone by compensating transactions (e.g. cancel the appointment, refund the payment). In choreography, services react to each other's events with no central coordinator — simple, but harder to track. In orchestration, a central orchestrator tells each service what to do — clearer flow and easier monitoring. Use the Outbox pattern so the DB update and the event publish are consistent.

### 9. What are distributed logging and tracing?
**Short:** A correlation or trace ID flows across services so logs and traces can be linked for one request.

**Brief:** The gateway generates a trace ID that is propagated via headers (W3C traceparent); Micrometer Tracing (formerly Spring Cloud Sleuth) adds traceId and spanId to the logging MDC automatically. Traces are sent to Zipkin or Jaeger to view the call tree and latency per service, while logs are centralised in ELK/EFK or Splunk and searched by trace ID. Metrics go to Prometheus and Grafana.

### 10. What happens if one service is down?
**Short:** With proper resilience, callers fail fast with fallbacks instead of cascading failures.

**Brief:** Without protection, threads in calling services block on timeouts, pools get exhausted and the failure cascades. With timeouts, circuit breakers and fallbacks, callers degrade gracefully — for example, showing cached data. Async messaging queues the events until the service recovers. Kubernetes restarts unhealthy pods based on health checks, and multiple replicas avoid single points of failure.

### 11. How do services authenticate with each other?
**Short:** Propagate the user's JWT, or use the OAuth2 client credentials flow for service identity; mTLS at the network level.

**Brief:** For user-initiated calls, the gateway validates the token and services forward the JWT so downstream services know the user and roles. For background service-to-service calls, each service obtains its own token from the authorization server using client credentials. A service mesh like Istio can add mutual TLS so only trusted services can communicate.

### 12. What is database-per-service, and what are its challenges?
**Short:** Each service owns its own database, and others access the data only through its API or events.

**Brief:** It ensures loose coupling and independent schema changes and scaling. Challenges are no cross-service joins (use API composition or CQRS read models), distributed transactions (Saga), data duplication and eventual consistency, and reporting across services (use a data warehouse or event streaming).

### 13. How do you do load balancing between services?
**Short:** Client-side with Spring Cloud LoadBalancer, or server-side with Kubernetes Services or a load balancer.

**Brief:** With Eureka, Feign and Spring Cloud LoadBalancer pick an instance using round-robin or random. In Kubernetes, a Service object distributes traffic across healthy pods, and an Ingress or cloud load balancer distributes external traffic. Health checks ensure traffic goes only to ready instances.

### 14. Explain your project's microservice architecture end to end.
**Short:** Walk from the client through the gateway, services, data, messaging, deployment and monitoring.

**Brief:** Template to adapt: "The React frontend calls an API Gateway (Spring Cloud Gateway) that validates the JWT from our identity provider and routes requests. Behind it we had about X services — for example user, order and notification — each a Spring Boot app with its own database (PostgreSQL/MySQL). Services talk synchronously through Feign with Resilience4j circuit breakers, and asynchronously through Kafka for events like 'order created'. Configuration came from Config Server or ConfigMaps. We built with Maven and Jenkins/GitHub Actions, packaged as Docker images and deployed to Kubernetes on AWS/Azure. Logs went to ELK with correlation IDs, and we monitored with Prometheus and Grafana. My role was developing service X and React screens Y, and I improved Z." Prepare a diagram you can draw.

## 12. Security (Preferred)

### 1. How does Spring Security work?
**Short:** A chain of servlet filters authenticates the request and stores the user in the SecurityContext, then authorization rules are applied.

**Brief:** A DelegatingFilterProxy delegates to the SecurityFilterChain. Filters like BearerTokenAuthenticationFilter or UsernamePasswordAuthenticationFilter extract credentials, the AuthenticationManager delegates to AuthenticationProviders (using UserDetailsService and PasswordEncoder, or a JWT decoder), and on success the Authentication is stored in the SecurityContextHolder (ThreadLocal). The AuthorizationFilter then checks URL rules, and `@PreAuthorize` checks method-level rules. In Spring Boot 3 we configure it with a SecurityFilterChain bean.

### 2. Compare authentication and authorization.
**Short:** Authentication verifies who you are; authorization decides what you can do.

**Brief:** Authentication happens first — via login credentials, a JWT or OAuth2 — and failure gives 401. Authorization checks roles, authorities or scopes, e.g. only a DOCTOR can view a medical record — failure gives 403. In Spring: `hasRole('ADMIN')` in config or `@PreAuthorize("hasAuthority('SCOPE_patient.read')")`.

### 3. What is JWT? Describe its structure and flow, and access vs. refresh tokens.
**Short:** A JSON Web Token is a signed, self-contained token with three Base64URL parts: header.payload.signature.

**Brief:** The header holds the algorithm (HS256, RS256), the payload holds claims (sub, roles, iat, exp — never sensitive data, since it is only encoded, not encrypted), and the signature verifies integrity. Flow: the user logs in, the server issues a token, the client sends `Authorization: Bearer <token>` on each request, and the server validates the signature and expiry without a session lookup. Access tokens are short-lived (e.g. 15 minutes) to limit damage if stolen; refresh tokens are long-lived, stored securely and used only to obtain new access tokens, and can be revoked.

### 4. What are OAuth 2.0 flows and roles?
**Short:** Roles: resource owner, client, authorization server and resource server. Main flows: authorization code with PKCE and client credentials.

**Brief:** OAuth 2.0 is an authorization framework for delegated access. The authorization code flow with PKCE is for user logins in web and SPA apps such as React; client credentials is for service-to-service; the implicit and password flows are deprecated. OpenID Connect adds an ID token for authentication on top. In Spring, our services act as resource servers (`spring-boot-starter-oauth2-resource-server`) validating JWTs from Okta, Azure AD or Keycloak.

### 5. Compare stateless and stateful authentication.
**Short:** Stateful stores the session on the server; stateless carries all identity in a token with each request.

**Brief:** Session-based auth stores a session ID in a cookie and data on the server, requiring sticky sessions or a shared store when scaling. Token-based auth (JWT) needs no server storage, scales horizontally and suits microservices, but tokens cannot easily be revoked before expiry, so we keep them short-lived. For REST APIs we set `SessionCreationPolicy.STATELESS`.

### 6. How do you store passwords?
**Short:** Hash with a slow, salted algorithm like BCrypt — never encrypt or store plain text.

**Brief:** BCrypt (or Argon2/PBKDF2) adds a random salt per password, defeating rainbow tables, and is intentionally slow to resist brute force. Use `BCryptPasswordEncoder` or `DelegatingPasswordEncoder` in Spring Security; verification uses `matches(raw, hash)`. Many enterprise apps delegate login entirely to an identity provider.

### 7. What are CSRF, XSS, and SQL injection, and how do you prevent them?
**Short:** CSRF tricks a logged-in browser into sending requests; XSS injects scripts into pages; SQL injection injects SQL via input.

**Brief:** CSRF: use CSRF tokens for cookie-based sessions and SameSite cookies; stateless JWT APIs using headers are not vulnerable, so CSRF is often disabled there. XSS: React escapes output by default, so avoid `dangerouslySetInnerHTML`, sanitise any HTML (DOMPurify), set a Content-Security-Policy and HttpOnly cookies. SQL injection: always use parameterised queries, PreparedStatement or JPA parameters, never concatenate user input, and validate input. Follow the OWASP Top 10.
## 13. React.js & JavaScript

### 1. Compare `var`, `let`, and `const`, and explain hoisting.
**Short:** var is function-scoped; let and const are block-scoped; const cannot be reassigned. Declarations are hoisted.

**Brief:** var declarations are hoisted and initialised as undefined, so using one before declaration gives undefined. let and const are hoisted too but stay in the Temporal Dead Zone until declared, so early access throws ReferenceError. const prevents reassignment, but object contents can still change. Function declarations are hoisted fully. I use const by default and let when reassignment is needed.

### 2. Explain closures, `this`, and arrow functions.
**Short:** A closure is a function remembering its outer scope; `this` depends on how a function is called; arrow functions take `this` from their surrounding scope.

**Brief:** Closures power data privacy, callbacks and React hooks — for example, a counter function returning an inner function that keeps access to `count`. In a regular function, `this` is the calling object (or undefined in strict mode, or set via call, apply or bind). Arrow functions have no own `this`, arguments or prototype, which makes them ideal for callbacks, but not for object methods that need `this`.

### 3. Compare Promises and async/await, and explain the event loop.
**Short:** Promises represent future values; async/await is cleaner syntax over them; the event loop runs callbacks when the call stack is empty.

**Brief:** A Promise is pending, fulfilled or rejected, chained with `.then`/`.catch`. async/await makes async code read like synchronous code, with try/catch for errors; `Promise.all` runs requests in parallel. JavaScript is single-threaded: synchronous code runs on the call stack, async callbacks wait in queues, and when the stack is empty the event loop runs all microtasks (promise callbacks) before the next macrotask (setTimeout, I/O). That is why a promise's `.then` runs before `setTimeout(fn, 0)`.

### 4. Compare `==` and `===`, and explain spread/rest and destructuring.
**Short:** `==` compares with type coercion, `===` without; spread expands, rest collects; destructuring unpacks values.

**Brief:** `'5' == 5` is true but `'5' === 5` is false, so always use `===`. Spread copies or merges: `const updated = { ...user, name: 'Pavan' }` and `[...arr, 4]`, essential for immutable state updates. Rest collects: `function sum(...nums)`. Destructuring: `const { name, age } = user;` and `const [count, setCount] = useState(0);`.

### 5. Explain the Virtual DOM and reconciliation.
**Short:** The Virtual DOM is a lightweight in-memory copy of the UI; reconciliation diffs old and new trees and updates only what changed in the real DOM.

**Brief:** Real DOM operations are expensive. On a state change, React re-renders components into a new virtual tree, diffs it against the previous one using heuristics (different element types replace the subtree; keys identify list items), and commits the minimal set of changes. React's Fiber architecture makes this work interruptible and prioritised.

### 6. Compare props and state, and functional and class components.
**Short:** Props are read-only inputs from the parent; state is data owned and changed by the component. Functional components with hooks are now standard.

**Brief:** Props flow one-way from parent to child and cannot be modified by the child; state is managed with useState, and updating it triggers a re-render. Class components use `this.state`, `setState` and lifecycle methods like componentDidMount; functional components are simpler, use hooks for state and side effects, and are recommended for all new code.

### 7. Explain the main hooks.
**Short:** useState for state, useEffect for side effects, useRef for mutable refs, useMemo/useCallback for memoisation, useContext for shared data.

**Brief:** `useState` returns a value and setter; use the functional update `setCount(c => c + 1)` when based on previous state. `useEffect(fn, deps)` runs after render: no array means every render, `[]` means once on mount, `[id]` means when id changes; return a cleanup function to unsubscribe, clear timers or abort fetches. `useRef` holds DOM references or values that persist without causing re-renders. `useMemo` caches a computed value; `useCallback` caches a function reference so memoised children do not re-render. `useContext` reads a context value without prop drilling.

### 8. What are the rules of hooks, and what are custom hooks?
**Short:** Call hooks only at the top level and only from React functions; custom hooks are reusable functions starting with "use".

**Brief:** Hooks must not be called inside loops, conditions or nested functions, because React relies on the call order to match state between renders; the ESLint plugin enforces this. Custom hooks extract reusable stateful logic — for example, `useFetch(url)` returning `{ data, loading, error }`, or `useDebounce` for search inputs.

### 9. Compare Context API and Redux.
**Short:** Context shares values through the tree without prop drilling; Redux is a predictable global state container with tooling.

**Brief:** Context suits low-frequency global data like the logged-in user, theme or locale, but every consumer re-renders when the value changes. Redux (Redux Toolkit) suits large apps with complex shared state, providing a single store, actions, reducers, middleware and DevTools. Server data is often better handled with React Query or RTK Query, which add caching and refetching.

### 10. How do you call an API and handle loading and errors?
**Short:** Call it inside useEffect (or a data library), tracking loading, data and error state.

**Brief:**
```jsx
function PatientList() {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  useEffect(() => {
    const controller = new AbortController();
    axios.get('/api/patients', { signal: controller.signal })
      .then(res => setData(res.data))
      .catch(err => { if (!axios.isCancel(err)) setError(err.message); })
      .finally(() => setLoading(false));
    return () => controller.abort();
  }, []);
  if (loading) return <Spinner />;
  if (error) return <ErrorMessage text={error} />;
  return <ul>{data.map(p => <li key={p.id}>{p.name}</li>)}</ul>;
}
```
In projects I use an Axios instance with interceptors to attach the JWT and handle 401s globally.

### 11. Why do lists need keys?
**Short:** Keys let React identify which items changed, were added or removed.

**Brief:** During reconciliation, React matches list items by key. Stable unique keys (like database ids) let it reuse elements correctly. Using the array index as a key causes bugs when items are reordered, inserted or deleted — for example, input values stay attached to the wrong rows — and causes unnecessary re-renders.

### 12. What is lifting state up, and what is prop drilling?
**Short:** Lifting state up moves shared state to the closest common parent; prop drilling is passing props through many intermediate components.

**Brief:** When two siblings need the same data, such as a filter and a list, the state lives in their parent and is passed down along with setter callbacks. When data must pass through many layers that do not use it, that is prop drilling, solved with Context, Redux or component composition.

### 13. How do you optimize React performance?
**Short:** Avoid unnecessary re-renders with React.memo, useMemo and useCallback; split code with lazy loading.

**Brief:** `React.memo` skips re-rendering when props are unchanged, used with useCallback and useMemo for stable references. `React.lazy` with `Suspense` lazy-loads routes, reducing bundle size. Use virtualization (react-window) for long lists, debounce search inputs, keep state local where possible, avoid creating objects inline for memoised children, and measure with React DevTools Profiler and Lighthouse before optimising.

### 14. Compare controlled and uncontrolled components.
**Short:** Controlled inputs are driven by React state; uncontrolled inputs keep their own value in the DOM, read via ref.

**Brief:** A controlled input uses `value={name}` and `onChange={e => setName(e.target.value)}`, enabling instant validation and formatting. Uncontrolled inputs use `defaultValue` and `useRef`, which is simpler and faster for large forms. React Hook Form uses the uncontrolled approach for performance; Formik is typically controlled.

### 15. Explain React Router basics.
**Short:** It provides client-side routing with BrowserRouter, Routes, Route, Link and hooks.

**Brief:** In v6: `<BrowserRouter><Routes><Route path="/patients/:id" element={<PatientDetail />} /></Routes></BrowserRouter>`. Navigate with `<Link to="/patients">` or `useNavigate()`, read parameters with `useParams()` and query strings with `useSearchParams()`. Nested routes use `<Outlet />`, and protected routes are wrapped in a component that checks authentication and redirects with `<Navigate to="/login" />`.

## 14. SQL & Database

### 1. What are the types of joins?
**Short:** INNER, LEFT, RIGHT, FULL OUTER, CROSS and SELF joins.

**Brief:** INNER JOIN returns only matching rows from both tables. LEFT JOIN returns all left rows plus matches (NULL where none) — e.g. all patients with or without appointments. RIGHT JOIN is the mirror. FULL OUTER JOIN returns all rows from both (MySQL lacks it; use a UNION of LEFT and RIGHT). CROSS JOIN gives a Cartesian product. SELF JOIN joins a table to itself, e.g. employees with their managers.

### 2. Write a query for the second- or Nth-highest salary.
**Short:** Use DENSE_RANK, or a subquery with MAX, or LIMIT/OFFSET.

**Brief:**
```sql
-- Second highest
SELECT MAX(salary) FROM employee WHERE salary < (SELECT MAX(salary) FROM employee);
-- Nth highest (handles ties)
SELECT DISTINCT salary FROM (
  SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk FROM employee) t
WHERE rnk = 3;
-- MySQL / PostgreSQL
SELECT DISTINCT salary FROM employee ORDER BY salary DESC LIMIT 1 OFFSET 2;
```

### 3. Find duplicate records, and delete duplicates.
**Short:** GROUP BY with HAVING COUNT > 1 to find; ROW_NUMBER to delete all but one.

**Brief:**
```sql
SELECT email, COUNT(*) FROM patient GROUP BY email HAVING COUNT(*) > 1;

DELETE FROM patient WHERE id IN (
  SELECT id FROM (
    SELECT id, ROW_NUMBER() OVER (PARTITION BY email ORDER BY id) AS rn FROM patient) t
  WHERE rn > 1);
```

### 4. Find the highest salary per department.
**Short:** GROUP BY department with MAX, or a window function to get the employee details.

**Brief:**
```sql
SELECT dept_id, MAX(salary) FROM employee GROUP BY dept_id;

SELECT * FROM (
  SELECT e.*, RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk FROM employee e) t
WHERE rnk = 1;
```

### 5. Compare WHERE and HAVING, and GROUP BY and ORDER BY.
**Short:** WHERE filters rows before grouping; HAVING filters groups after aggregation. GROUP BY groups rows; ORDER BY sorts the result.

**Brief:** WHERE cannot use aggregate functions; HAVING can, e.g. `HAVING COUNT(*) > 5`. Logical order of execution: FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT. That is why you cannot use a SELECT alias in WHERE in most databases.

### 6. Compare DELETE, TRUNCATE, and DROP.
**Short:** DELETE removes chosen rows; TRUNCATE removes all rows quickly; DROP removes the whole table.

**Brief:** DELETE is DML, supports WHERE, fires triggers, logs each row and can be rolled back. TRUNCATE is DDL, removes all rows by deallocating pages, is much faster, resets identity and usually cannot be rolled back (except in PostgreSQL and SQL Server within a transaction). DROP deletes the table structure, data, indexes and constraints.

### 7. Explain indexes and when they hurt.
**Short:** Indexes (usually B-trees) speed up lookups; clustered sets the physical row order, non-clustered is a separate structure pointing to rows.

**Brief:** A table has one clustered index (often the primary key) and can have many non-clustered ones. Indexes help WHERE, JOIN and ORDER BY on selective columns; composite indexes follow the leftmost-prefix rule; covering indexes avoid table lookups. They hurt by slowing INSERT, UPDATE and DELETE and using storage; they are useless on low-cardinality columns, and they are bypassed when you wrap the column in a function (`WHERE UPPER(name) = ...`) or use a leading wildcard (`LIKE '%abc'`).

### 8. Explain ACID properties and isolation levels.
**Short:** Atomicity, Consistency, Isolation and Durability; isolation levels trade consistency against concurrency.

**Brief:** Atomicity: all or nothing. Consistency: constraints hold. Isolation: concurrent transactions do not interfere. Durability: committed data survives crashes. Anomalies: dirty read (reading uncommitted data), non-repeatable read (the same row changes between reads) and phantom read (new rows appear in a repeated query). READ UNCOMMITTED allows all three; READ COMMITTED (default in PostgreSQL, Oracle, SQL Server) prevents dirty reads; REPEATABLE READ (default in MySQL InnoDB) also prevents non-repeatable reads; SERIALIZABLE prevents all.

### 9. Explain normalization.
**Short:** Organising tables to reduce redundancy: 1NF atomic values, 2NF no partial dependency, 3NF no transitive dependency.

**Brief:** 1NF: each column holds atomic values with no repeating groups. 2NF: 1NF plus every non-key column depends on the whole composite key. 3NF: 2NF plus non-key columns depend only on the key, not on other non-key columns (e.g. move department name into a Department table). BCNF is a stricter 3NF. Denormalisation is sometimes done deliberately for read performance in reporting.

### 10. Compare a subquery and a correlated subquery.
**Short:** A normal subquery runs once independently; a correlated subquery references the outer query and runs per row.

**Brief:** `WHERE salary > (SELECT AVG(salary) FROM employee)` runs once. Correlated: `SELECT * FROM employee e WHERE salary > (SELECT AVG(salary) FROM employee WHERE dept_id = e.dept_id)` evaluates for each outer row, which can be slow; it can often be rewritten with JOINs or window functions. EXISTS with a correlated subquery is efficient for existence checks.

### 11. Explain window functions.
**Short:** They compute values across related rows without collapsing them, using OVER (PARTITION BY ... ORDER BY ...).

**Brief:** For salaries 100, 90, 90, 80: ROW_NUMBER gives 1, 2, 3, 4 (unique); RANK gives 1, 2, 2, 4 (gaps after ties); DENSE_RANK gives 1, 2, 2, 3 (no gaps). Others include LAG/LEAD (previous/next row), running totals with `SUM() OVER (ORDER BY date)`, and NTILE. They are used for top-N per group, deduplication and comparisons.

### 12. How do you optimize a slow query?
**Short:** Check the execution plan, add the right indexes, and reduce the data scanned.

**Brief:** Run EXPLAIN (or EXPLAIN ANALYZE) to look for full table scans, bad join orders and missing indexes. Add or adjust indexes on filter and join columns; avoid `SELECT *`; avoid functions on indexed columns; replace correlated subqueries with joins; use pagination; keep statistics updated; and batch operations. From the application side, fix N+1 queries, add caching and use connection pooling (HikariCP). Enable the slow query log to find problem queries.

### 13. Compare primary, foreign, and unique keys.
**Short:** A primary key uniquely identifies a row and is not null; a foreign key references another table's key; a unique key enforces uniqueness but allows null.

**Brief:** A table has one primary key (possibly composite), automatically indexed. Foreign keys enforce referential integrity, with options like ON DELETE CASCADE. A table can have multiple unique constraints, like email or MRN number; most databases allow NULLs in unique columns.

## 15. Testing & Tools

### 1. Explain JUnit 5 annotations.
**Short:** `@Test`, `@BeforeEach`, `@AfterEach`, `@BeforeAll`, `@AfterAll`, `@ParameterizedTest`, `@DisplayName`, `@Disabled` and `@Nested`.

**Brief:** `@BeforeEach` sets up before every test, while `@BeforeAll` runs once and must be static. `@ParameterizedTest` with `@ValueSource`, `@CsvSource` or `@MethodSource` runs the same test with multiple inputs. Assertions include `assertEquals`, `assertThrows`, `assertAll` and `assertTimeout`; AssertJ's fluent `assertThat` is also popular. JUnit 5 = Platform + Jupiter + Vintage.

### 2. Explain Mockito: `@Mock`, `@InjectMocks`, `when().thenReturn()`, `verify()`, and spy vs. mock.
**Short:** @Mock creates a fake dependency; @InjectMocks injects mocks into the class under test; when/thenReturn stubs; verify checks interactions. A spy wraps a real object.

**Brief:**
```java
@ExtendWith(MockitoExtension.class)
class PatientServiceTest {
    @Mock PatientRepository repo;
    @InjectMocks PatientService service;
    @Test void findsPatient() {
        when(repo.findById(1L)).thenReturn(Optional.of(new Patient(1L, "Ravi")));
        assertEquals("Ravi", service.getPatient(1L).getName());
        verify(repo, times(1)).findById(1L);
    }
}
```
A mock returns defaults for unstubbed methods; a spy calls real methods unless stubbed (use `doReturn().when(spy)` for spies). ArgumentCaptor captures arguments passed to mocks.

### 3. Compare unit and integration testing. What are `@SpringBootTest`, `@WebMvcTest`, and `@DataJpaTest`?
**Short:** Unit tests isolate one class with mocks; integration tests check components working together. The annotations load all or part of the Spring context.

**Brief:** `@SpringBootTest` loads the full context, used for end-to-end integration tests, often with Testcontainers for a real database. `@WebMvcTest(PatientController.class)` loads only the web layer, with MockMvc and `@MockBean` (`@MockitoBean` in Boot 3.4+) for services — testing status codes, JSON and validation. `@DataJpaTest` loads only JPA components with an embedded database, rolling back after each test. Slices are faster than a full context.

### 4. How do you test a void method or an exception?
**Short:** Verify interactions for void methods; use assertThrows for exceptions.

**Brief:** For a void method, use `verify(emailClient).send(any())` or an ArgumentCaptor to check what was passed, or assert state changes. To stub a void method to throw: `doThrow(new RuntimeException()).when(mock).delete(1L)`. For exceptions: `PatientNotFoundException ex = assertThrows(PatientNotFoundException.class, () -> service.getPatient(99L)); assertEquals("Patient not found", ex.getMessage());`.

### 5. Compare Git merge and rebase. What are cherry-pick and stash? How do you resolve conflicts?
**Short:** Merge combines branches with a merge commit; rebase replays commits on top for a linear history. Cherry-pick copies a specific commit; stash saves uncommitted work temporarily.

**Brief:** Merge preserves history and is safe for shared branches. Rebase gives a cleaner history but rewrites commits, so never rebase public or shared branches. Cherry-pick is used to bring a hotfix commit to a release branch. `git stash` and `git stash pop` let you switch branches with unfinished changes. To resolve conflicts: pull the latest, open the conflict markers, decide the correct code with the other developer if needed, `git add`, then continue the merge or rebase, and run the tests.

### 6. What are the Maven lifecycle phases, scopes and dependency conflicts?
**Short:** validate, compile, test, package, verify, install and deploy; scopes include compile, provided, runtime and test.

**Brief:** Running a phase runs all earlier phases — `mvn clean install` cleans, compiles, tests, packages and installs to the local repository. Scopes: compile (default, everywhere), provided (supplied by the container, like servlet-api), runtime (only needed at runtime, like JDBC drivers) and test (JUnit). Conflicts between transitive versions are resolved by "nearest wins"; inspect with `mvn dependency:tree`, fix with `<exclusions>` or pin versions in `<dependencyManagement>` (Spring Boot's parent BOM does this).

### 7. Describe the CI/CD pipeline in your project.
**Short:** Commit → build and test → code quality scan → Docker image → deploy to environments.

**Brief:** Template to adapt: "On a pull request, Jenkins or GitHub Actions runs `mvn clean verify` with unit and integration tests, JaCoCo coverage and a SonarQube quality gate, plus `npm test` and a build for React. After merge, it builds a Docker image, scans it for vulnerabilities, pushes it to a registry (ECR/ACR/Artifactory), and deploys to dev automatically, then to QA and production with approvals, using Helm or ArgoCD on Kubernetes. Rollback is done by redeploying the previous image tag."

### 8. What are Docker and Kubernetes basics?
**Short:** A Docker image is a packaged template; a container is a running instance. Kubernetes orchestrates containers with pods, deployments and services.

**Brief:** A Dockerfile defines the image, e.g. `FROM eclipse-temurin:17-jre`, `COPY target/app.jar app.jar`, `ENTRYPOINT ["java","-jar","/app.jar"]`, with multi-stage builds to keep it small. A Pod is the smallest unit in Kubernetes, holding one or more containers. A Deployment manages replicas, rolling updates and rollback. A Service gives a stable network endpoint and load balancing. ConfigMaps and Secrets hold configuration, liveness and readiness probes use Actuator health, and the HPA autoscales on CPU or metrics.

### 9. Explain Kafka basics, and compare Kafka and RabbitMQ.
**Short:** Kafka is a distributed event log with topics, partitions, consumer groups and offsets; RabbitMQ is a traditional message broker.

**Brief:** Producers write to topics, which are split into partitions for parallelism; ordering is guaranteed only within a partition, chosen by message key (e.g. patientId). Consumers in a group share partitions — each partition goes to one consumer in the group — and track progress with offsets. Messages are retained for a configured time, so they can be replayed. Kafka suits high-throughput event streaming, event sourcing and log pipelines; RabbitMQ suits complex routing with exchanges, per-message acknowledgment and task queues, but messages are removed after consumption. In Spring we use KafkaTemplate and `@KafkaListener`, with idempotent consumers and dead-letter topics for failures.

## 16. Scenario / Project-Based Questions (KP US Round)

### 1. Walk me through your current project's architecture.
**Short:** Give a 2-minute overview: business purpose, architecture, tech stack, your role and an impact.

**Brief:** Structure it as follows. First, what the application does and for whom (one line). Second, the architecture from client to database: React UI, API Gateway, the key microservices, databases, Kafka and the cloud and deployment setup. Third, your responsibilities — features you built end to end, APIs, React screens and tests. Fourth, one or two measurable achievements, like "reduced API latency by 40%" or "raised coverage from 55% to 80%". Be ready for deep follow-up questions on anything you mention.

### 2. What was the most challenging bug you fixed in production?
**Short:** Use the STAR format: Situation, Task, Action and Result.

**Brief:** Example to adapt: "After a release, an API intermittently timed out under load. Logs and thread dumps showed the HikariCP connection pool was exhausted because a new method held a transaction open while calling an external service. I moved the external call outside the transaction, added a timeout and circuit breaker, and added a metric alert on pool usage. Latency returned to normal and we added a code-review checklist item for long transactions." Pick a real story with clear technical depth.

### 3. Your API is slow. How would you debug it end to end?
**Short:** Measure first, find the slow layer using tracing and metrics, then fix the bottleneck.

**Brief:** Check whether it is all requests or specific ones, and since when (a recent deploy?). Use distributed tracing (Zipkin/Jaeger) or APM to see the time per span: gateway, service, DB or downstream calls. Check the DB: SQL logs, N+1 queries, EXPLAIN plans and missing indexes. Check the JVM: GC pauses, CPU and thread dumps for blocked threads, plus connection and thread pool saturation. Check the network and external dependencies. Then fix with indexes, caching (Redis), parallel calls, pagination, async processing or scaling, and verify with load tests (JMeter/Gatling).

### 4. How do you handle a high volume of requests?
**Short:** Scale horizontally, cache, process asynchronously and protect the system with rate limiting.

**Brief:** Keep services stateless so Kubernetes can add replicas (HPA). Cache hot reads in Redis, use CDNs for static React assets, and use database read replicas and connection pooling. Offload heavy work to Kafka consumers, apply rate limiting and backpressure at the gateway, use circuit breakers and bulkheads to isolate failures, and load test to know your limits.

### 5. How would you design a patient appointment booking system?
**Short:** Services for patients, doctors' schedules, appointments and notifications, with concurrency control on slots.

**Brief:** Requirements: search doctors and slots, book, cancel or reschedule, and send notifications. Design: React UI → API Gateway with OAuth2 → Patient Service, Provider/Schedule Service, Appointment Service and Notification Service, each with its own database. The key challenge is preventing double-booking: use a unique constraint on (doctor_id, slot_time), or optimistic locking on the slot row, or a short-lived slot hold with expiry. Booking publishes an "AppointmentBooked" event to Kafka; Notification sends SMS or email and reminders. Use Redis to cache slot availability, idempotency keys for booking requests, audit logs and encryption of PHI for HIPAA compliance, and time zone handling with UTC storage.

### 6. How would you prevent duplicate payments or orders?
**Short:** Use idempotency keys plus database unique constraints.

**Brief:** The client generates a unique Idempotency-Key per operation and sends it in a header. The server stores the key with the result; a repeated request with the same key returns the stored result instead of processing again. Back it with a unique constraint on the key or the business identifier to handle race conditions. Disable the submit button on the UI after the first click, and make Kafka consumers idempotent by tracking processed message IDs.

### 7. How do you ensure code quality in your team?
**Short:** Code reviews, automated tests, static analysis and agreed standards.

**Brief:** Every change goes through a pull request with at least one reviewer; we follow coding standards and checklists; CI runs unit and integration tests with a coverage threshold; SonarQube checks bugs, code smells and security issues with a quality gate; we use linters and formatters (Checkstyle, ESLint, Prettier); and we refactor regularly and document key decisions.

### 8. Describe a disagreement in a code review and how you handled it.
**Short:** Focus on data and the codebase's goals, discuss respectfully and reach a decision.

**Brief:** Example to adapt: "A reviewer suggested a complex caching layer for an endpoint, while I thought it was premature. Instead of arguing in comments, I set up a quick call, shared metrics showing the endpoint's low traffic and latency, and we agreed to add monitoring and revisit if needed. We documented the decision. It kept the code simple and the relationship good." Show listening, data-driven reasoning and openness to being wrong.

### 9. How do you handle sensitive data such as PII or health data?
**Short:** Minimise it, encrypt it in transit and at rest, restrict access, mask it in logs and audit all access.

**Brief:** Use TLS for all traffic and encryption at rest (DB, backups, disks), with field-level encryption for highly sensitive fields like SSN. Apply role-based least-privilege access and audit every read and write of PHI. Never log PHI or tokens — mask values in logs (e.g. a Logback masking pattern) and in non-production data. Do not return more data than needed in APIs, keep secrets in a vault, and follow HIPAA requirements — especially relevant at Kaiser Permanente.

### 10. Why Kaiser Permanente? Are you comfortable with the 2–11 PM shift?
**Short:** Mission-driven healthcare at scale plus strong engineering, and yes, the shift enables collaboration with US teams.

**Brief:** Example: "Kaiser Permanente is one of the largest integrated healthcare organisations in the US, so the software I build directly impacts care for millions of members. The GCC offers modern technology like Spring Boot microservices, React and cloud, at enterprise scale with strong engineering practices, and a chance to grow. Yes, I am comfortable with the 2–11 PM shift; overlapping with US hours means faster communication and better collaboration with the US stakeholders." Answer the shift question honestly.
