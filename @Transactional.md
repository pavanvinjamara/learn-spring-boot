`@Transactional` in Spring Framework is used to manage **database transactions automatically**.

Think of transaction like:

> “Either all DB operations succeed OR everything rollback.”

Very important in banking, payments, orders, inventory, etc.

---

# Simple Real Example

Imagine:

```java
transferMoney(from, to, amount)
```

Flow:

1. Deduct money from A
2. Add money to B

If server crashes after deducting from A but before adding to B:

❌ Money lost

So transaction ensures:

✅ Both happen
OR
✅ Nothing happens

---

# Without @Transactional

```java
public void transfer() {
    withdraw();
    deposit();
}
```

If `deposit()` fails:

* withdraw already committed
* inconsistent data

---

# With @Transactional

```java
@Transactional
public void transfer() {
    withdraw();
    deposit();
}
```

Now Spring creates one transaction.

If any exception happens:

✅ rollback everything automatically

---

# Internal Working Flow

## Step-by-step

When request comes:

```java
@Transactional
public void createOrder() {
    saveOrder();
    savePayment();
}
```

Spring does:

1. Open DB transaction
2. Execute all queries
3. If success → COMMIT
4. If exception → ROLLBACK

---

# Real Backend Example

## Order Service

```java
@Service
public class OrderService {

    @Autowired
    private OrderRepository orderRepo;

    @Autowired
    private PaymentRepository paymentRepo;

    @Transactional
    public void placeOrder(Order order, Payment payment) {

        orderRepo.save(order);

        paymentRepo.save(payment);

    }
}
```

---

# What Happens in DB

## Success

```sql
INSERT INTO orders;
INSERT INTO payments;
COMMIT;
```

---

## Failure

Suppose payment fails.

```sql
INSERT INTO orders;
INSERT INTO payments; -- failed
ROLLBACK;
```

Order also removed automatically.

---

# Why Companies Use It

In real applications:

* Banking
* Payment
* Inventory
* Ticket booking
* Wallet transfer
* E-commerce order

Data consistency is critical.

---

# Interview Explanation (3 Years Experience Style)

You can say:

> "`@Transactional` is used to maintain data consistency across multiple database operations.
> In our applications, we use it in service layer methods where multiple inserts or updates should behave as a single unit of work.
> If any operation fails, Spring automatically rolls back the complete transaction to avoid partial data updates."

---

# Where We Usually Put It

Mostly:

✅ Service layer

```java
@Service
public class UserService {

    @Transactional
    public void registerUser() {

    }
}
```

Because service layer contains business logic.

---

# Important Concepts

# 1. Atomicity

Either complete fully OR rollback fully.

ACID property.

---

# 2. Commit

Save changes permanently.

---

# 3. Rollback

Undo all changes.

---

# 4. Isolation

Multiple transactions should not affect each other improperly.

---

# Example with Exception

```java
@Transactional
public void createUser() {

    userRepo.save(user);

    int x = 10 / 0;

    profileRepo.save(profile);
}
```

Exception occurs:

```java
10 / 0
```

Result:

✅ user save also rollback

Nothing inserted.

---

# Very Important Interview Point

## By Default Rollback Happens Only For:

✅ RuntimeException
✅ Unchecked Exception

NOT for checked exception.

---

# Example

```java
@Transactional
public void test() throws Exception {

}
```

Checked exception may NOT rollback.

---

# To Rollback for Checked Exception

```java
@Transactional(rollbackFor = Exception.class)
```

Example:

```java
@Transactional(rollbackFor = Exception.class)
public void createOrder() throws Exception {

}
```

---

# propagation in @Transactional

Controls transaction behavior between methods.

Most common:

## REQUIRED (default)

```java
@Transactional
```

Uses existing transaction or creates new one.

---

## REQUIRES_NEW

Creates completely new transaction.

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
```

Used for:

* audit logs
* notification saving
* independent DB operations

---

# Real Scenario

```java
placeOrder()
    -> saveOrder()
    -> saveAudit()
```

Even if order fails:

audit should save.

So:

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
```

for audit method.

---

# Isolation Levels

Used to handle concurrent transactions.

Common:

| Isolation       | Meaning                |
| --------------- | ---------------------- |
| READ_COMMITTED  | Most used              |
| REPEATABLE_READ | Prevents dirty updates |
| SERIALIZABLE    | Highest safety, slower |

Example:

```java
@Transactional(isolation = Isolation.READ_COMMITTED)
```

---

# Timeout

```java
@Transactional(timeout = 5)
```

If transaction takes more than 5 seconds:

rollback.

---

# Read Only Optimization

```java
@Transactional(readOnly = true)
```

Used for fetch APIs.

Benefits:

* better performance
* avoids unnecessary locks

Example:

```java
@Transactional(readOnly = true)
public List<User> getUsers() {

}
```

---

# Common Mistakes

# 1. Using on private methods

❌ Won’t work properly

```java
@Transactional
private void save() {

}
```

Because Spring uses proxy mechanism.

---

# 2. Calling same class method internally

```java
this.saveData();
```

Transaction may not apply.

Because proxy bypass happens.

---

# 3. Long Transactions

Bad practice:

```java
@Transactional
public void process() {

    callExternalAPI(); // slow

}
```

Keeps DB connection locked.

Avoid long-running logic inside transaction.

---

# Best Practice

✅ Keep transaction small

✅ Use in service layer

✅ Avoid external API calls inside transaction

✅ Use readOnly=true for GET APIs

✅ Handle exceptions properly

---

# How Spring Internally Works

Spring uses:

* AOP Proxy
* Transaction Manager
* Hibernate/JPA Session

Flow:

```text
Client
  ↓
Spring Proxy
  ↓
Open Transaction
  ↓
Service Method
  ↓
Commit / Rollback
```

---

# Real Project Example

Suppose your Zygal app stores:

* AI detection event
* alert logs
* video metadata

All should save together.

```java
@Transactional
public void saveDetectionData() {

    saveEvent();

    saveAlert();

    saveMetadata();
}
```

If metadata save fails:

✅ everything rollback

avoids inconsistent records.

---

# One-Line Definition

> "`@Transactional` is used to make multiple database operations execute as a single unit of work with automatic commit and rollback support."
