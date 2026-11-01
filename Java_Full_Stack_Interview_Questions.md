# Java Full Stack Developer – Interview Question Bank

**Role:** Java Full Stack Developer (3–5 yrs) · Kaiser Permanente GCC, Pune (Hinjewadi Phase 2)
**Stack:** Java, Spring Boot, Microservices, React.js, REST APIs, SQL

**Priority tip:** For a 3–5 year role, the most frequently asked topics are HashMap internals, Java 8 stream coding, multithreading with the Executor Framework and CompletableFuture, Spring Boot annotations and exception handling, `@Transactional`, the N+1 problem, microservice communication and circuit breakers, SQL queries for salaries and duplicates, and React hooks. Start with those.

## 1. Core Java Basics
1. What are JDK, JRE, and JVM, and how do they differ?
2. Why is Java platform-independent? Is the JVM platform-independent?
3. Explain JVM architecture: class loader, runtime data areas, and execution engine.
4. What is the difference between stack and heap memory?
5. What is the String pool? Why is String immutable?
6. Compare String, StringBuilder, and StringBuffer.
7. What is the difference between `==` and `.equals()`?
8. What is the hashCode/equals contract? What breaks if you override only one?
9. What are `final`, `finally`, and `finalize`?
10. What does the `static` keyword do? Can you override a static method?
11. What are wrapper classes, autoboxing, and unboxing? Explain the Integer cache (-128 to 127).
12. Is Java pass-by-value or pass-by-reference?
13. What is the difference between shallow copy and deep copy, and how does `clone()` work?
14. How do you create an immutable class?
15. What are serialization, `transient`, and `serialVersionUID`?
16. What are the access modifiers, and what is the default access level?
17. How does garbage collection work? Name the types of GC (G1, ZGC, Parallel).
18. What are strong, weak, soft, and phantom references?
19. What are the causes of memory leaks in Java, and how do you detect them?
20. What is the difference between OutOfMemoryError and StackOverflowError?

## 2. OOP Concepts
1. What are the four pillars of OOP? Give a real-world example of each.
2. What is the difference between an abstract class and an interface after Java 8?
3. Compare method overloading and overriding. Can you overload by return type only?
4. Compare compile-time and runtime polymorphism.
5. Why does Java not support multiple inheritance with classes? How do interfaces solve the diamond problem?
6. When should you use composition instead of inheritance?
7. What are covariant return types?
8. Can a constructor be private? When would you use one?
9. Explain constructor chaining with `this()` and `super()`.
10. What are the SOLID principles? Explain each with an example.
11. Explain association, aggregation, and composition.
12. Can you override a private or final method?

## 3. Collections Framework
1. Describe the Collection hierarchy. Why does Map not extend Collection?
2. Compare ArrayList and LinkedList, and say when to use each.
3. Compare ArrayList and Vector, and Array and ArrayList.
4. **How does HashMap work internally?** Cover hashing, buckets, collisions, treeification in Java 8, load factor, and rehashing. This is asked almost every time.
5. What happens when two keys have the same hashCode?
6. Compare HashMap, Hashtable, ConcurrentHashMap, and SynchronizedMap.
7. How does ConcurrentHashMap work internally (segments vs. CAS in Java 8)?
8. Compare HashMap, LinkedHashMap, and TreeMap.
9. Compare HashSet, LinkedHashSet, and TreeSet. How does HashSet work internally?
10. Compare Comparable and Comparator.
11. Compare fail-fast and fail-safe iterators, and explain ConcurrentModificationException.
12. How do you make a collection read-only or synchronized?
13. Why should HashMap keys be immutable? What happens with a mutable key?
14. Explain PriorityQueue, Deque, and ArrayDeque.
15. Explain the internals of CopyOnWriteArrayList.
16. How do you sort a HashMap by value?
17. Compare WeakHashMap and IdentityHashMap.

## 4. Exception Handling
1. Describe the exception hierarchy: Throwable, Error, and Exception.
2. Compare checked and unchecked exceptions, with examples.
3. Compare `throw` and `throws`.
4. Explain try-with-resources and AutoCloseable.
5. Can `finally` be skipped? Does it run if there is a return in try?
6. How do you create a custom exception, and should it be checked or unchecked?
7. What are the rules for exceptions when overriding methods?
8. What are exception chaining and multi-catch?
9. What are the best practices for exception handling in a real project?

## 5. Java 8+ Features
1. What are lambda expressions and functional interfaces?
2. Explain the built-in functional interfaces: Predicate, Function, Consumer, Supplier, and BiFunction.
3. What are method references, and what are their types?
4. What is the Stream API? Compare intermediate and terminal operations.
5. Compare `map` and `flatMap`.
6. Compare streams and collections, and sequential and parallel streams.
7. Explain lazy evaluation in streams.
8. What are Optional's methods (`orElse` vs `orElseGet`), and what are its best practices?
9. What are default and static methods in interfaces?
10. What are the new Date/Time API classes (LocalDate, LocalDateTime)?
11. Explain `Collectors.groupingBy`, `partitioningBy`, `toMap`, and `joining`.
12. What are `reduce`, `findFirst`, `anyMatch`, `distinct`, and `sorted`?
13. What features came after Java 8: records, sealed classes, `var`, text blocks, switch expressions, and virtual threads (Java 21)?

### Common Stream Coding Questions
1. Find duplicate elements in a list.
2. Find the second-highest number or salary.
3. Count the frequency of each character in a string.
4. Group employees by department and find the highest salary per department.
5. Find the first non-repeating character.
6. Sort employees by salary, then by name.
7. Join a list of strings with a comma.
8. Find the average salary by department.
9. Separate even and odd numbers.
10. Convert a List to a Map, and handle duplicate keys.

## 6. Multithreading & Concurrency
The JD emphasizes this area, so prepare it deeply.

1. Compare a process and a thread.
2. What are the ways to create a thread? Compare Thread and Runnable, and Runnable and Callable.
3. What is the thread lifecycle and its states?
4. Compare `start()` and `run()`.
5. Compare `sleep()` and `wait()`, and `notify()` and `notifyAll()`.
6. Compare the `synchronized` method and the synchronized block. What is the difference between a class-level and an object-level lock?
7. What is the `volatile` keyword? Compare `volatile` and `synchronized`.
8. What are deadlock, livelock, and starvation? How do you prevent a deadlock?
9. What are race conditions and thread safety?
10. Explain the Executor Framework: ExecutorService, ThreadPoolExecutor, and the types of thread pools.
11. Compare `submit()` and `execute()`, and `shutdown()` and `shutdownNow()`.
12. Explain Future and CompletableFuture, and chaining with `thenApply`, `thenCompose`, and `thenCombine`.
13. Compare ReentrantLock and synchronized. What are ReadWriteLock and tryLock?
14. What are atomic classes (AtomicInteger), and what is CAS?
15. What are CountDownLatch, CyclicBarrier, and Semaphore?
16. What is ThreadLocal, and how is it used in web apps?
17. What are daemon threads?
18. Explain the producer-consumer problem using wait/notify and using BlockingQueue.
19. What is the Java Memory Model, and what is happens-before?
20. What are the thread pool parameters (core size, max size, queue, rejection policy)?
21. Write a program that prints odd and even numbers using two threads.
22. Where have you used multithreading in your project? This one is very likely.

## 7. Design Patterns & DSA
1. Write a thread-safe Singleton using double-checked locking or an enum. How can Singleton be broken (reflection, serialization, cloning)?
2. Explain Factory vs. Abstract Factory.
3. Explain Builder, Strategy, Observer, Adapter, Decorator, Proxy, and Template patterns.
4. Which design patterns does Spring use internally (Singleton, Proxy, Factory, Template)?
5. What microservice patterns do you know: Circuit Breaker, Saga, API Gateway, CQRS?

### Coding (DSA) Questions
1. Reverse a string or check for a palindrome.
2. Check if two strings are anagrams.
3. Find the missing number in an array.
4. Solve the two-sum problem.
5. Find the longest substring without repeating characters.
6. Reverse a linked list and detect a cycle.
7. Validate balanced parentheses using a stack.
8. Merge two sorted arrays.
9. Find the largest and second-largest elements without sorting.
10. Implement an LRU cache using LinkedHashMap.
11. Explain Big-O for common collection operations.

## 8. Spring & Spring Boot
1. Compare Spring and Spring Boot. What are Spring Boot's advantages?
2. What are IoC and Dependency Injection? Compare constructor, setter, and field injection. Which is preferred and why?
3. What does `@SpringBootApplication` contain internally?
4. How does auto-configuration work?
5. What are bean scopes (singleton, prototype, request, session)?
6. What is the bean lifecycle (`@PostConstruct`, `@PreDestroy`)?
7. Compare `@Component`, `@Service`, `@Repository`, and `@Controller`.
8. Compare `@Controller` and `@RestController`.
9. Compare `@Autowired`, `@Qualifier`, and `@Primary`.
10. What are `@Value`, `@ConfigurationProperties`, profiles, and `application.yml`?
11. What are Spring Boot starters, and what is Actuator?
12. How do you handle circular dependencies?
13. What is global exception handling (`@ControllerAdvice`, `@ExceptionHandler`)?
14. How do you do request validation (`@Valid`, `@NotNull`, custom validators)?
15. Compare `@Transactional` propagation and isolation levels. Why might `@Transactional` not work on a self-invoked method?
16. What is AOP? Explain advice, pointcut, and join point, and give logging as an example.
17. Explain the DispatcherServlet flow in Spring MVC.
18. Compare filters and interceptors.
19. How do you change the default port, or exclude an auto-configuration?
20. Compare `CommandLineRunner` and `ApplicationRunner`.

## 9. REST APIs
1. What are REST principles? Compare REST and SOAP.
2. Compare the HTTP methods. Which are idempotent? Compare PUT and PATCH.
3. What are the important status codes: 200, 201, 204, 400, 401, 403, 404, 409, 500?
4. Compare `@PathVariable` and `@RequestParam`, and `@RequestBody` and `ResponseEntity`.
5. What are the strategies for API versioning?
6. How do you implement pagination and sorting?
7. How do you secure a REST API?
8. What is CORS, and how do you handle it in Spring Boot?
9. Compare RestTemplate, WebClient, and Feign Client.
10. How do you document APIs (Swagger/OpenAPI)?

## 10. JPA / Hibernate
1. Compare JPA, Hibernate, and Spring Data JPA.
2. Compare CrudRepository, JpaRepository, and PagingAndSortingRepository.
3. What are entity states (transient, persistent, detached, removed)?
4. Compare lazy and eager loading. What is LazyInitializationException?
5. **What is the N+1 problem, and how do you fix it** (fetch join, `@EntityGraph`, batch size)?
6. What are the relationship mappings (`@OneToMany`, `@ManyToOne`, `@ManyToMany`), `mappedBy`, and cascade types?
7. Explain first-level vs. second-level cache.
8. Compare `get()` and `load()`, and `save()`, `persist()`, and `merge()`.
9. What are derived query methods, `@Query`, JPQL, and native queries?
10. Compare optimistic and pessimistic locking (`@Version`).
11. What are the ID generation strategies?
12. What is dirty checking?

## 11. Microservices
1. Compare monolithic and microservices architecture, with pros and cons.
2. How do you decide service boundaries?
3. Compare synchronous (REST/Feign) and asynchronous (Kafka) communication.
4. What is service discovery (Eureka)?
5. What does an API Gateway do (Spring Cloud Gateway)?
6. What is centralized configuration (Spring Cloud Config)?
7. What are Circuit Breaker, Resilience4j, retry, fallback, and timeout?
8. How do you handle distributed transactions? Explain the Saga pattern (choreography vs. orchestration).
9. What are distributed logging and tracing (correlation ID, Sleuth/Micrometer, Zipkin, ELK)?
10. What happens if one service is down?
11. How do services authenticate with each other?
12. What is database-per-service, and what are its challenges?
13. How do you do load balancing between services?
14. Explain your project's microservice architecture end to end. Prepare this one well.

## 12. Security (Preferred)
1. How does Spring Security work (filter chain)?
2. Compare authentication and authorization.
3. What is JWT? Describe its structure and the flow, and the difference between a refresh token and an access token.
4. What are OAuth 2.0 flows and roles?
5. Compare stateless and stateful authentication.
6. How do you store passwords (BCrypt)?
7. What are CSRF, XSS, and SQL injection, and how do you prevent them? Healthcare companies care about this.

## 13. React.js & JavaScript
1. Compare `var`, `let`, and `const`, and explain hoisting.
2. Explain closures, `this`, and arrow functions.
3. Compare Promises and async/await, and explain the event loop.
4. Compare `==` and `===`, and explain the spread/rest operators and destructuring.
5. Explain the Virtual DOM and reconciliation.
6. Compare props and state. Compare functional and class components.
7. Explain `useState`, `useEffect` (dependency array, cleanup), `useRef`, `useMemo`, `useCallback`, and `useContext`.
8. What are the rules of hooks, and what are custom hooks?
9. Compare Context API and Redux.
10. How do you call an API using Axios or fetch, and handle loading and errors?
11. Why do lists need keys?
12. What is lifting state up, and what is prop drilling?
13. How do you optimize performance (`React.memo`, lazy loading, code splitting)?
14. Compare controlled and uncontrolled components.
15. Explain React Router basics.

## 14. SQL & Database
1. What are the types of joins? Give an example of each.
2. Write a query for the second- or Nth-highest salary.
3. Find duplicate records, and delete duplicates.
4. Find the highest salary per department.
5. Compare WHERE and HAVING, and GROUP BY and ORDER BY.
6. Compare DELETE, TRUNCATE, and DROP.
7. Explain indexes (clustered vs. non-clustered) and when indexes hurt.
8. Explain ACID properties and isolation levels (dirty read, phantom read).
9. Explain normalization (1NF, 2NF, 3NF).
10. Compare a subquery and a correlated subquery.
11. Explain window functions (`ROW_NUMBER`, `RANK`, `DENSE_RANK`).
12. How do you optimize a slow query (EXPLAIN plan)?
13. Compare primary, foreign, and unique keys.

## 15. Testing & Tools
1. Explain JUnit 5 annotations (`@Test`, `@BeforeEach`, `@ParameterizedTest`).
2. Explain Mockito's `@Mock`, `@InjectMocks`, `when().thenReturn()`, `verify()`, and spy vs. mock.
3. Compare unit and integration testing. What do `@SpringBootTest`, `@WebMvcTest`, and `@DataJpaTest` do?
4. How do you test a void method or an exception?
5. Compare Git merge and rebase. What are `cherry-pick` and `stash`? How do you resolve conflicts?
6. What are the Maven lifecycle phases? What are scopes and dependency conflicts?
7. Describe the CI/CD pipeline in your project.
8. What are Docker basics (image vs. container, Dockerfile)? What are Kubernetes pods and deployments?
9. Explain Kafka basics: topics, partitions, consumer groups, offsets. Compare Kafka and RabbitMQ.

## 16. Scenario / Project-Based Questions (KP US Round)
1. Walk me through your current project's architecture.
2. What was the most challenging bug you fixed in production?
3. Your API is slow. How would you debug it end to end?
4. How do you handle a high volume of requests?
5. How would you design a patient appointment booking system? Expect a healthcare angle.
6. How would you prevent duplicate payments or orders (idempotency)?
7. How do you ensure code quality in your team?
8. Describe a disagreement in a code review and how you handled it.
9. How do you handle sensitive data such as PII or health data (masking, encryption, logging)?
10. Why Kaiser Permanente? Are you comfortable with the 2–11 PM shift?
