## 1. Brief Introduction — “Tell me about your tech stack and your current project.”

“I’m a Full Stack Developer with around 2+ years of experience.
I mainly work with Java, Spring Boot, Microservices, Kafka, MySQL/MongoDB on the backend and React/Vue.js on the frontend.

In my current project, we developed surveillance and AI-based monitoring applications.
My responsibilities include:

* Developing REST APIs using Spring Boot
* Implementing authentication using JWT
* Working with Kafka for asynchronous communication
* Optimizing APIs and database queries
* Handling frontend integration
* Dockerizing services for deployment

We follow microservice architecture where services like user management, alerts, AI detection, and reporting communicate independently.”

---

# 2. Microservices vs Monolith

## Monolith

Everything is in one application:

* UI
* Backend
* Database
* Business logic

### Problems

* Hard to scale
* One bug can affect entire app
* Deployment becomes risky
* Large codebase difficult to maintain

---

## Microservices

Application is divided into small independent services.

Example:

* User Service
* Order Service
* Payment Service
* Notification Service

Each service:

* Has its own logic
* Can be deployed independently
* Can use its own database

---

## Advantages of Microservices

* Independent deployment
* Better scalability
* Fault isolation
* Easier maintenance
* Technology flexibility

---

## Challenges

* Distributed system complexity
* Network latency
* Service communication
* Monitoring/logging
* Deployment complexity

---

# 3. Kafka vs REST API Communication

## Kafka → Asynchronous Communication

Producer sends message to Kafka topic.
Consumer processes later.

### Used for:

* Notifications
* Emails
* Logs
* Analytics
* Event-driven architecture

### Benefits

* Loose coupling
* High scalability
* Retry support

---

## REST API → Synchronous Communication

One service directly calls another service and waits for response.

Example:
Order Service → Payment Service

---

# 4. How to Call Another REST API Synchronously

## Approaches

### 1. RestTemplate (Old)

```java
RestTemplate restTemplate = new RestTemplate();

String response = restTemplate.getForObject(
    "http://payment-service/pay",
    String.class
);
```

---

### 2. WebClient (Modern & Preferred)

```java
WebClient webClient = WebClient.create();

String response = webClient.get()
        .uri("http://payment-service/pay")
        .retrieve()
        .bodyToMono(String.class)
        .block();
```

### Why WebClient?

* Non-blocking
* Better performance
* Reactive support

---

### 3. OpenFeign

Most common in microservices.

```java
@FeignClient(name = "payment-service")
public interface PaymentClient {

    @GetMapping("/pay")
    String pay();
}
```

### Benefits

* Cleaner code
* Easy integration
* Load balancing support

---

# 5. Which Client Libraries Have You Used?

“In my projects I used:

* WebClient for external API calls
* OpenFeign for microservice-to-microservice communication
* RestTemplate in legacy code”

---

# 6. Deployment of Java Applications

## Basic Flow

1. Build JAR using Maven
2. Create Docker image
3. Push image to registry
4. Deploy using Kubernetes or server

---

## Maven Build

```bash
mvn clean package
```

Produces:

```bash
target/app.jar
```

---

## Docker

```dockerfile
FROM openjdk:17
COPY target/app.jar app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

---

## Kubernetes

Used for:

* Auto scaling
* Self healing
* Load balancing
* Rolling updates

---

# 7. AWS / Docker / Kubernetes

“Yes, I have worked with Docker for containerization and basic Kubernetes deployment concepts.

I’m familiar with AWS services like:

* EC2
* S3
* RDS
* IAM

Docker helps package applications consistently, and Kubernetes helps manage containers at scale.”

---

# 8. HashMap vs LinkedHashMap

| Feature            | HashMap    | LinkedHashMap             |
| ------------------ | ---------- | ------------------------- |
| Order              | No order   | Maintains insertion order |
| Performance        | Faster     | Slightly slower           |
| Internal Structure | Hash table | Hash table + linked list  |

---

## Example

```java
Map<Integer,String> map = new HashMap<>();
```

Output order may change.

```java
Map<Integer,String> map = new LinkedHashMap<>();
```

Maintains insertion order.

---

# 9. SOLID Principles

## S — Single Responsibility

One class → one responsibility.

---

## O — Open/Closed

Open for extension, closed for modification.

---

## L — Liskov Substitution

Child class should replace parent safely.

---

## I — Interface Segregation

Don’t force unnecessary methods.

---

## D — Dependency Inversion

Depend on abstraction, not concrete classes.

---

# 10. Checked vs Unchecked Exceptions

## Checked Exception

Checked at compile time.

Example:

```java
IOException
SQLException
```

Must handle using try-catch or throws.

---

## Unchecked Exception

Occurs at runtime.

Example:

```java
NullPointerException
ArithmeticException
ArrayIndexOutOfBoundsException
```

---

# 11. throw vs throws

| throw                            | throws              |
| -------------------------------- | ------------------- |
| Used to throw exception manually | Declares exception  |
| Inside method                    | In method signature |

---

## Example

```java
throw new RuntimeException("Error");
```

```java
public void test() throws IOException
```

---

# 12. Polymorphism

One object behaves in multiple forms.

## Types

### Compile Time

Method overloading

### Runtime

Method overriding

---

# 13. Functional Interface

Interface with only one abstract method.

Example:

```java
@FunctionalInterface
interface MyInterface {
    void print();
}
```

Used with:

* Lambda expressions
* Streams

Examples:

* Runnable
* Comparator
* Callable

---

# 14. Comparable vs Comparator

| Comparable      | Comparator     |
| --------------- | -------------- |
| Natural sorting | Custom sorting |
| compareTo()     | compare()      |
| Inside class    | Separate class |

---

## Comparable

```java
class Employee implements Comparable<Employee>
```

---

## Comparator

```java
Collections.sort(list, comparator);
```

---

# 15. Garbage Collection

GC automatically removes unused objects from heap memory.

---

## Common GC Algorithms

### Serial GC

Single thread

### Parallel GC

Multiple threads

### G1 GC

Balanced GC for large applications

### ZGC/Shenandoah

Low latency collectors

---

# 16. Stack vs Heap Memory

| Stack               | Heap           |
| ------------------- | -------------- |
| Stores method calls | Stores objects |
| Faster              | Larger memory  |
| Thread specific     | Shared         |

---

# 17. Immutable Class

Object state cannot change after creation.

Example:

```java
String
```

---

## Why String Immutable?

* Security
* Thread safety
* String pool optimization
* Hashcode caching

---

# 18. Optional in Java

Avoids NullPointerException.

```java
Optional<String> name = Optional.of("Pavan");
```

---

## Methods

```java
isPresent()
orElse()
orElseThrow()
```

---

# 19. @Qualifier Annotation

Used when multiple beans of same type exist.

```java
@Autowired
@Qualifier("paypalService")
private PaymentService paymentService;
```

---

# 20. Spring Bean Scopes

## Singleton

One object for entire application.

Default scope.

Used for:

* Services
* Repositories

---

## Prototype

New object every request.

Used when object state changes frequently.

---

# 21. PUT vs PATCH

| PUT                      | PATCH                   |
| ------------------------ | ----------------------- |
| Full update              | Partial update          |
| Replaces entire resource | Updates specific fields |

---

# 22. HTTP Status Codes

| Code | Meaning               |
| ---- | --------------------- |
| 200  | Success               |
| 201  | Created               |
| 400  | Bad Request           |
| 401  | Unauthorized          |
| 403  | Forbidden             |
| 404  | Not Found             |
| 500  | Internal Server Error |

---

# 23. Hibernate get() vs load()

| get()                  | load()           |
| ---------------------- | ---------------- |
| Immediately hits DB    | Lazy loading     |
| Returns null if absent | Throws exception |
| Eager                  | Proxy object     |

---

# 24. Build Tools

“I mainly use Maven.”

Purpose:

* Dependency management
* Build automation
* Packaging

---

# 25. Spring Cloud

Used in microservices for:

* Service discovery
* API gateway
* Config server
* Load balancing

Common tools:

* Eureka
* Spring Cloud Gateway
* Config Server
* OpenFeign

---

# 26. Securing REST APIs

## Common Approaches

* JWT Authentication
* Spring Security
* Role-based authorization
* HTTPS
* API Gateway security

---

# 27. Spring Boot Experience

“Yes, I have extensive experience with Spring Boot for:

* REST API development
* Security
* JPA/Hibernate
* Kafka integration
* Exception handling
* Microservices”

---

# 28. Coding Pattern Problem

Example input:

```text
3a2b1c
```

Output:

```text
aaabbc
```

## Solution

```java
public class Main {

    public static void main(String[] args) {

        String input = "3a2b1c";
        StringBuilder result = new StringBuilder();

        for (int i = 0; i < input.length(); i += 2) {

            int count = input.charAt(i) - '0';
            char ch = input.charAt(i + 1);

            for (int j = 0; j < count; j++) {
                result.append(ch);
            }
        }

        System.out.println(result);
    }
}
```

---

# 29. “Do You Have Any Questions for Us?”

Good questions:

* “What does the current architecture look like?”
* “What are the biggest technical challenges in the team?”
* “What technologies are planned in upcoming projects?”
* “How do teams handle deployments and monitoring?”
* “What are the expectations for this role in the first 3 months?”
