# 02 - Pillars of OOP

The four pillars are useful only when tied to system evolution and maintainability.

---

## 1) Encapsulation = invariant safety at boundaries
Encapsulation is not “private everything”; it is controlled state transitions.

```java
class Subscription {
    enum Status { TRIAL, ACTIVE, SUSPENDED, CANCELED }

    private Status status = Status.TRIAL;

    public void activate() {
        if (status == Status.CANCELED) throw new IllegalStateException("cannot activate canceled subscription");
        status = Status.ACTIVE;
    }

    public void suspend() {
        if (status != Status.ACTIVE) throw new IllegalStateException("only active can be suspended");
        status = Status.SUSPENDED;
    }

    public Status status() { return status; }
}
```

**Why it matters:** protects invariants, reduces illegal states, simplifies debugging.

---

## 2) Abstraction = stable contract, replaceable implementation

```java
interface ExchangeRateProvider {
    double rate(String base, String quote);
}

final class CachedRateProvider implements ExchangeRateProvider {
    private final ExchangeRateProvider upstream;

    CachedRateProvider(ExchangeRateProvider upstream) {
        this.upstream = upstream;
    }

    @Override
    public double rate(String base, String quote) {
        // cache omitted for brevity
        return upstream.rate(base, quote);
    }
}
```

**Why it matters:** isolates volatility and enables extension without invasive rewrites.

---

## 3) Inheritance = constrained reuse (use sparingly)
Inheritance is useful when subtype semantics are truly substitutable.

```java
abstract class JobRunner {
    public final void runJob() {
        validate();
        execute();
        audit();
    }

    protected void validate() {}
    protected abstract void execute();
    protected void audit() {}
}
```

**Why it matters:** useful for shared workflows, but deep hierarchies become brittle.

---

## 4) Polymorphism = extension without branching explosion

```java
sealed interface PaymentCommand permits CardPayment, WalletPayment {}
record CardPayment(String cardToken, long cents) implements PaymentCommand {}
record WalletPayment(String walletId, long cents) implements PaymentCommand {}

final class PaymentService {
    void process(PaymentCommand cmd) {
        if (cmd instanceof CardPayment c) {
            // process card
        } else if (cmd instanceof WalletPayment w) {
            // process wallet
        }
    }
}
```

**Why it matters:** reduces `if/else` growth and localizes behavior changes.

---

## Summary
- Encapsulation → protect invariants
- Abstraction → isolate change
- Inheritance → reuse with caution
- Polymorphism → scale behavior cleanly
