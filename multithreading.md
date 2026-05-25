# Multithreading in Java — From Basic to Advanced

Think of a program like a restaurant kitchen.

* **Single-threaded program** = only one cook working.
* **Multithreading** = multiple cooks working together at the same time.

In backend applications like Spring Boot APIs, multithreading helps:

* handle many users simultaneously
* process tasks faster
* improve API response time
* use CPU efficiently

---

# 1. What is a Thread?

A **thread** is the smallest unit of execution inside a process.

Example:

* Your Java application = Process
* Inside it:

  * Thread 1 handles API request
  * Thread 2 handles DB call
  * Thread 3 sends email

---

# 2. Single Thread vs Multithreading

## Single Thread

```java
public class Main {
    public static void main(String[] args) {

        task1();
        task2();
    }

    static void task1() {
        System.out.println("Task 1");
    }

    static void task2() {
        System.out.println("Task 2");
    }
}
```

Execution:

```text
Task 1
Task 2
```

One after another.

---

## Multithreading

```java
class MyThread extends Thread {

    public void run() {
        System.out.println("Thread Running");
    }
}

public class Main {

    public static void main(String[] args) {

        MyThread t1 = new MyThread();

        t1.start();
    }
}
```

Output:

```text
Thread Running
```

`start()` creates a new thread.

---

# 3. Thread Lifecycle

A thread goes through states:

```text
NEW → RUNNABLE → RUNNING → WAITING/BLOCKED → TERMINATED
```

## Explanation

| State      | Meaning                    |
| ---------- | -------------------------- |
| NEW        | Thread object created      |
| RUNNABLE   | Ready to run               |
| RUNNING    | CPU executing              |
| WAITING    | Waiting for another thread |
| BLOCKED    | Waiting for lock           |
| TERMINATED | Finished                   |

---

# 4. Ways to Create Threads

## Method 1 — Extending Thread

```java
class MyThread extends Thread {

    public void run() {
        System.out.println("Running...");
    }
}
```

---

## Method 2 — Implement Runnable (Most Used)

```java
class MyTask implements Runnable {

    public void run() {
        System.out.println("Task Running");
    }
}

public class Main {

    public static void main(String[] args) {

        Thread t1 = new Thread(new MyTask());

        t1.start();
    }
}
```

Why preferred?

* Java supports only single inheritance
* Better design

---

# 5. Runnable vs Thread

| Thread                      | Runnable                 |
| --------------------------- | ------------------------ |
| Class                       | Interface                |
| Tight coupling              | Loose coupling           |
| Cannot extend another class | Can extend another class |
| Less preferred              | Most preferred           |

Interview answer:

> In real projects we generally use Runnable or ExecutorService instead of directly extending Thread because it provides better scalability and separation of task and execution.

---

# 6. `start()` vs `run()`

## Wrong

```java
t1.run();
```

Normal method call.

No new thread created.

---

## Correct

```java
t1.start();
```

Creates separate thread.

---

# 7. Multiple Threads Example

```java
class Task1 implements Runnable {

    public void run() {

        for(int i=1; i<=5; i++) {
            System.out.println("Task1 : " + i);
        }
    }
}

class Task2 implements Runnable {

    public void run() {

        for(int i=1; i<=5; i++) {
            System.out.println("Task2 : " + i);
        }
    }
}

public class Main {

    public static void main(String[] args) {

        Thread t1 = new Thread(new Task1());
        Thread t2 = new Thread(new Task2());

        t1.start();
        t2.start();
    }
}
```

Output order may change because threads run independently.

---

# 8. Sleep Method

Pauses thread.

```java
Thread.sleep(2000);
```

Means wait 2 seconds.

Example:

```java
for(int i=1; i<=5; i++) {

    System.out.println(i);

    Thread.sleep(1000);
}
```

---

# 9. Join Method

Waits for thread completion.

```java
t1.join();
```

Example:

```java
t1.start();

t1.join();

System.out.println("Completed");
```

Main thread waits until `t1` finishes.

---

# 10. Thread Priority

```java
t1.setPriority(Thread.MAX_PRIORITY);
```

Values:

* 1 → MIN
* 5 → NORMAL
* 10 → MAX

But modern OS may ignore priorities.

---

# 11. Daemon Thread

Background thread.

Example:

* Garbage Collector

```java
t1.setDaemon(true);
```

Daemon thread stops when main thread ends.

---

# 12. Race Condition

Most important concept.

## Problem

Two threads updating same variable simultaneously.

```java
class Counter {

    int count = 0;

    void increment() {
        count++;
    }
}
```

Two threads may corrupt value.

Expected:

```text
2000
```

Actual:

```text
1876
```

because:

```text
count++
```

is NOT atomic.

Internally:

```text
Read
Modify
Write
```

---

# 13. Synchronization

Used to avoid race conditions.

## Example

```java
class Counter {

    int count = 0;

    synchronized void increment() {
        count++;
    }
}
```

Now only one thread enters method at a time.

---

# 14. What is Lock?

When thread enters synchronized block:

* acquires lock
* other threads wait

```java
synchronized(this) {

}
```

---

# 15. Static Synchronization

Lock on class object.

```java
synchronized static void test() {

}
```

---

# 16. Deadlock

Two threads waiting for each other forever.

## Example

Thread 1:

* lock A
* waiting for B

Thread 2:

* lock B
* waiting for A

Application hangs.

---

# 17. Avoiding Deadlock

Common strategies:

* Maintain lock order
* Use timeout locks
* Reduce nested synchronization

---

# 18. Inter Thread Communication

Methods:

* `wait()`
* `notify()`
* `notifyAll()`

Used inside synchronized block.

---

## Producer Consumer Example

```java
class Shared {

    synchronized void produce() throws Exception {

        System.out.println("Produced");

        wait();

        System.out.println("Resumed");
    }

    synchronized void consume() {

        System.out.println("Consumed");

        notify();
    }
}
```

---

# 19. Executor Service (Very Important)

In real applications we do NOT create threads manually.

We use thread pools.

## Why?

Creating threads is expensive.

---

## Thread Pool

Pool reuses threads.

```text
Task → Thread Pool → Worker Thread
```

---

## Example

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Main {

    public static void main(String[] args) {

        ExecutorService service =
                Executors.newFixedThreadPool(3);

        for(int i=1; i<=10; i++) {

            int num = i;

            service.submit(() -> {
                System.out.println(
                    Thread.currentThread().getName()
                    + " " + num
                );
            });
        }

        service.shutdown();
    }
}
```

---

# 20. Why Thread Pool Important in Backend

Example:

* 1000 users hit API
* Creating 1000 threads = expensive

Instead:

* Maintain fixed pool
* Reuse threads

This is how:

* Tomcat
* Spring Boot
* Kafka consumers
* DB connection pools

work internally.

---

# 21. Callable vs Runnable

| Runnable                       | Callable            |
| ------------------------------ | ------------------- |
| No return value                | Returns value       |
| Cannot throw checked exception | Can throw exception |

---

## Callable Example

```java
Callable<Integer> task = () -> {

    return 10 + 20;
};
```

---

# 22. Future

Used to get async result.

```java
Future<Integer> future = service.submit(task);

Integer result = future.get();
```

`get()` waits for completion.

---

# 23. CompletableFuture (Very Important)

Modern async programming.

Used heavily in:

* microservices
* API parallel calls
* async processing

---

## Example

```java
CompletableFuture.supplyAsync(() -> {

    return "Hello";

}).thenApply(result -> {

    return result + " World";

}).thenAccept(System.out::println);
```

---

# 24. Real Backend Example

Suppose dashboard API needs:

* user data
* orders
* payments

Sequential:

```text
1 sec + 1 sec + 1 sec = 3 sec
```

Parallel using multithreading:

```text
1 sec total
```

Example:

```java
CompletableFuture<User> user =
    CompletableFuture.supplyAsync(() -> getUser());

CompletableFuture<List<Order>> orders =
    CompletableFuture.supplyAsync(() -> getOrders());

CompletableFuture<Payment> payment =
    CompletableFuture.supplyAsync(() -> getPayment());

CompletableFuture.allOf(user, orders, payment).join();
```

---

# 25. Volatile Keyword

Used for visibility between threads.

```java
volatile boolean running = true;
```

If one thread changes value:
other threads immediately see updated value.

---

# 26. Atomic Variables

Better than synchronization for counters.

```java
AtomicInteger count = new AtomicInteger();
```

Increment:

```java
count.incrementAndGet();
```

Thread-safe and faster.

---

# 27. Concurrent Collections

Normal collections are not thread-safe.

Use:

* `ConcurrentHashMap`
* `CopyOnWriteArrayList`

Example:

```java
ConcurrentHashMap<Integer, String> map =
        new ConcurrentHashMap<>();
```

---

# 28. Parallel Stream

Java streams using multithreading.

```java
list.parallelStream()
    .forEach(System.out::println);
```

Uses ForkJoinPool internally.

---

# 29. ForkJoinPool

Used for divide-and-conquer tasks.

Example:

* large data processing
* recursive computations

---

# 30. Virtual Threads (Java 21+)

Traditional threads are expensive.

Virtual threads are lightweight.

1000000\ \text{virtual threads} \gg 1000\ \text{platform threads}

Example:

```java
Thread.startVirtualThread(() -> {

    System.out.println("Virtual Thread");
});
```

Benefits:

* handles massive concurrency
* low memory usage
* ideal for microservices

---

# 31. Multithreading in Spring Boot

## Async Method

```java
@EnableAsync
```

```java
@Async
public CompletableFuture<String> getData() {

    return CompletableFuture.completedFuture("Data");
}
```

Runs in separate thread.

---

# 32. Important Interview Questions

## What is multithreading?

> Multithreading allows multiple threads to execute concurrently within a process to improve performance and responsiveness.

---

## Difference between process and thread?

| Process         | Thread        |
| --------------- | ------------- |
| Heavyweight     | Lightweight   |
| Separate memory | Shared memory |
| Slower          | Faster        |

---

## Why synchronization needed?

> To avoid race conditions when multiple threads access shared resources simultaneously.

---

## Why ExecutorService preferred?

> Because manual thread creation is expensive. ExecutorService provides thread pooling, better resource management, scalability, and task lifecycle handling.

---

## Difference between Callable and Runnable?

> Callable returns result and throws checked exceptions, Runnable does not.

---

# 33. What Real Companies Use

In backend systems:

* API requests handled by thread pools
* Async email sending
* Kafka consumers
* Payment processing
* Parallel microservice calls
* Background jobs
* File processing

Frameworks:

* Spring Boot
* Apache Kafka
* Tomcat
* Amazon Web Services

all heavily use multithreading internally.

---

# Learning Order Recommendation

Learn in this order:

1. Thread basics
2. Runnable
3. Synchronization
4. Race condition
5. ExecutorService
6. Callable + Future
7. CompletableFuture
8. Concurrent collections
9. Virtual threads

---

# Most Important Concepts for Interviews

Focus strongly on:

* Race condition
* Synchronization
* Thread pool
* ExecutorService
* CompletableFuture
* Deadlock
* AtomicInteger
* Volatile
* Async API calls

These are most asked for 2–4 years experience backend developers.
