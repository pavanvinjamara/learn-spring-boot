`@CircuitBreaker` is used in Spring Boot (commonly with Resilience4j) to protect your application from repeatedly calling a failing external service.

Think like this:

* Your API calls another service (payment service, user service, third-party API)
* That service becomes slow or down
* Without circuit breaker → your application keeps trying again and again
* Threads get blocked → application becomes slow/crashes

Circuit Breaker stops calling the failing service temporarily.

# Real-Life Example

Suppose your Order Service calls Payment Service.

```java
public String makePayment() {
    return paymentClient.pay();
}
```

If Payment Service is down:

* Every request waits
* Timeout happens
* Server threads become busy
* API response becomes very slow

So we use:

```java
@CircuitBreaker(name = "paymentService", fallbackMethod = "fallbackResponse")
```

---

# Simple Flow

### Normal State (Closed)

Everything works.

```text
Order Service ---> Payment Service
```

Requests go normally.

---

### Failure Starts

Payment service starts failing.

Circuit breaker counts failures.

Example:

* 5 requests failed out of 10
* Failure threshold crossed

Then circuit becomes OPEN.

---

### Open State

Now calls are blocked immediately.

Instead of waiting for timeout:

```text
"Payment service temporarily unavailable"
```

Fallback method executes directly.

This protects your application.

---

### Half Open State

After some time:

Circuit breaker allows a few requests to test.

If successful:

* circuit closes again

If still failing:

* circuit opens again

---

# Interview Explanation (3 Years Experience Style)

“We used Circuit Breaker with Resilience4j for external API communication to avoid cascading failures.

If a downstream service becomes slow or unavailable, circuit breaker prevents repeated calls and returns fallback responses immediately. This improves system stability and reduces thread blocking and timeout issues.”

---

# Complete Example

## Dependency

Using [Resilience4j Spring Boot documentation](https://resilience4j.readme.io/docs/getting-started-3?utm_source=chatgpt.com#configuration)

```xml
<dependency>
    <groupId>io.github.resilience4j</groupId>
    <artifactId>resilience4j-spring-boot3</artifactId>
</dependency>
```

---

# Service Class

```java
@Service
public class PaymentService {

    @CircuitBreaker(
        name = "paymentService",
        fallbackMethod = "fallbackPayment"
    )
    public String callPaymentService() {

        System.out.println("Calling Payment API");

        // external API call
        return restTemplate.getForObject(
            "http://payment-api/pay",
            String.class
        );
    }

    public String fallbackPayment(Exception ex) {

        return "Payment service is down. Please try later.";
    }
}
```

---

# application.yml

```yaml
resilience4j:
  circuitbreaker:
    instances:
      paymentService:
        slidingWindowSize: 10
        failureRateThreshold: 50
        waitDurationInOpenState: 10s
        permittedNumberOfCallsInHalfOpenState: 3
```

---

# Important Configurations

## slidingWindowSize

How many requests to monitor.

```yaml
slidingWindowSize: 10
```

Checks last 10 requests.

---

## failureRateThreshold

Failure percentage to open circuit.

```yaml
failureRateThreshold: 50
```

If 5 out of 10 fail → open circuit.

---

## waitDurationInOpenState

How long to wait before retrying.

```yaml
waitDurationInOpenState: 10s
```

---

## permittedNumberOfCallsInHalfOpenState

How many test calls allowed.

```yaml
permittedNumberOfCallsInHalfOpenState: 3
```

---

# States of Circuit Breaker

```text
CLOSED  -> normal calls

OPEN    -> block calls

HALF_OPEN -> test few calls
```

---

# Commonly Used With

* Retry
* Timeout
* Fallback
* WebClient
* RestTemplate
* Microservices

---

# Example in Microservices

```text
Order Service
    |
    ---> Payment Service
    ---> User Service
    ---> Notification Service
```

If Notification Service fails:

* Circuit breaker prevents Order Service slowdown.

---

# Difference Between Retry and Circuit Breaker

| Retry                    | Circuit Breaker             |
| ------------------------ | --------------------------- |
| Tries again              | Stops calling               |
| Used for temporary issue | Used for continuous failure |
| Can increase load        | Reduces load                |

Usually both are used together.

---

# Simple Interview One-Line Answer

“Circuit Breaker is a fault tolerance pattern used to stop repeated calls to failing services and provide fallback responses, improving application stability and preventing cascading failures.”
