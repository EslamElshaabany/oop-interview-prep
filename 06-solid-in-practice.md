# 06 - SOLID in Practice

Treat SOLID as design heuristics under change pressure, not rigid laws.

## S — Single Responsibility Principle
A unit should have one reason to change in business terms.

```java
class InvoiceCalculator {
    long total(Invoice invoice) { return 0L; }
}

class InvoicePdfRenderer {
    byte[] render(Invoice invoice) { return new byte[0]; }
}
```

**Good practice:** separate policy, orchestration, and IO concerns.

---

## O — Open/Closed Principle
Extend behavior through new types/composition, not repeated edits to stable core.

```java
interface ShippingFeePolicy {
    long feeFor(Order order);
}

final class StandardShippingPolicy implements ShippingFeePolicy {
    public long feeFor(Order order) { return 1500; }
}
```

---

## L — Liskov Substitution Principle
Subtypes must preserve base-type expectations (behavioral contracts).

```java
interface Cache {
    String get(String key);
    void put(String key, String value);
}

// A subtype throwing UnsupportedOperationException for put() would violate expectations.
```

**Good practice:** reason in terms of preconditions/postconditions, not just inheritance syntax.

---

## I — Interface Segregation Principle
Split fat interfaces by client use-cases.

```java
interface UserReader {
    User findById(String id);
}

interface UserWriter {
    void save(User user);
}
```

---

## D — Dependency Inversion Principle
High-level policy depends on abstractions; low-level details are injected.

```java
interface PaymentGateway {
    PaymentResult charge(ChargeRequest request);
}

final class CheckoutService {
    private final PaymentGateway gateway;

    CheckoutService(PaymentGateway gateway) {
        this.gateway = gateway;
    }
}
```

---

## Practical framing
- SOLID improves changeability and testability.
- Over-application can create accidental complexity.
- The right depth depends on context and constraints.
