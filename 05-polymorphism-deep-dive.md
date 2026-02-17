# 05 - Polymorphism Deep Dive

Polymorphism is useful for **change isolation** and **behavioral extension**.

## 1) Runtime dispatch (overriding)
The invoked method depends on actual object type at runtime.

```java
interface TaxPolicy {
    long apply(long netCents);
}

final class UaeVatPolicy implements TaxPolicy {
    public long apply(long netCents) { return netCents + (netCents * 5 / 100); }
}

final class ZeroTaxPolicy implements TaxPolicy {
    public long apply(long netCents) { return netCents; }
}

final class InvoiceService {
    long totalWithTax(long netCents, TaxPolicy policy) {
        return policy.apply(netCents);
    }
}
```

This avoids country-specific branching in core billing flow.

---

## 2) Compile-time polymorphism (overloading)
Resolution happens at compile time based on declared parameter types.

```java
class AuditLogger {
    void log(String event) {}
    void log(String event, Throwable error) {}
}
```

Overloading improves API ergonomics, but should stay unambiguous.

---

## 3) Common pitfalls
- Overloading != overriding.
- `static` methods are hidden, not overridden.
- Field access is not polymorphic (method calls are).
- Generic type erasure can make overloaded signatures illegal.

```java
// Illegal in Java due to type erasure:
// void process(List<String> items) {}
// void process(List<Integer> items) {}
```

---

## 4) Performance note
Modern JVMs can devirtualize hot call sites when types are stable. Replacing polymorphism with manual branching too early usually hurts readability more than it helps performance.

## One-liner
"Use polymorphism where behavior varies by policy/domain variant and we want extension without editing stable orchestration code."
