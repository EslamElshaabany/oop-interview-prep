# 01 - Class vs Object

This topic is less about definitions and more about **identity**, **lifecycle**, and **invariants**.

## Framing
- **Class**: type contract + behavior boundary + construction policy.
- **Object**: runtime instance with identity and state over time.

## Entity vs Value Object
- **Entity**: identity matters (`Order#123` stays the same conceptual object as state changes).
- **Value Object**: value equality matters (`Money(10, "USD")` equals another with same value).

```java
import java.math.BigDecimal;
import java.util.Objects;
import java.util.UUID;

record Money(BigDecimal amount, String currency) {
    Money {
        if (amount.signum() < 0) throw new IllegalArgumentException("amount < 0");
        if (currency == null || currency.isBlank()) throw new IllegalArgumentException("currency required");
    }

    Money add(Money other) {
        if (!currency.equals(other.currency)) throw new IllegalArgumentException("currency mismatch");
        return new Money(amount.add(other.amount), currency);
    }
}

final class BankAccount {
    private final UUID id;   // identity
    private Money balance;   // mutable state behind invariant

    BankAccount(UUID id, Money openingBalance) {
        this.id = Objects.requireNonNull(id);
        this.balance = Objects.requireNonNull(openingBalance);
    }

    public UUID id() { return id; }
    public Money balance() { return balance; }

    public void deposit(Money amount) {
        this.balance = this.balance.add(amount);
    }
}
```

## Points worth calling out
- Identity semantics (`equals/hashCode`) should match domain intent.
- Mutability should be explicit and minimal; immutable value objects reduce bug surface.
- Encapsulated mutation is about invariant protection, not just `private` fields.

## One-liner
"A class defines safe behavior boundaries; objects carry identity and state transitions under those rules."
