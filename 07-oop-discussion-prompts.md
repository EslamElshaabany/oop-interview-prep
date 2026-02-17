# 07 - OOP Discussion Prompts

Use these prompts to rehearse concise, high-signal answers.

## 1) How do you decide between inheritance and composition?
**Strong answer includes:** substitutability, lifecycle coupling, and long-term change cost.

## 2) What does “good encapsulation” look like in production?
**Strong answer includes:** invariant-protecting methods, illegal-state prevention, and narrow mutation surface.

## 3) How is abstraction different from indirection for its own sake?
**Strong answer includes:** abstraction around volatility, measurable reduction in coupling.

## 4) When would you choose abstract class over interface?
**Strong answer includes:** skeletal implementation, shared state/workflow, controlled extension points.

## 5) Explain LSP with a real backend example.
**Strong answer includes:** behavioral contract, subtype expectations, avoiding runtime surprises.

## 6) What polymorphism pitfalls do you watch for in Java?
**Strong answer includes:** overload vs override confusion, static method hiding, erasure constraints.

## 7) How do you keep OOP models from becoming anemic?
**Strong answer includes:** domain behavior close to domain data, not all logic in service layer.

## 8) How do you model aggregate boundaries?
**Strong answer includes:** consistency boundaries, transactional updates, invariants.

## 9) How does OOP interact with concurrency concerns?
**Strong answer includes:** immutability where possible, thread-safe state transitions, ownership discipline.

## 10) How do you evolve public interfaces safely?
**Strong answer includes:** compatibility strategy, versioning/deprecation, contract tests.

## 11) Is SOLID always beneficial?
**Strong answer includes:** cost of abstraction, YAGNI, context-dependent depth.

## 12) How do you explain OOP value to a distributed-systems team?
**Strong answer includes:** bounded contexts, composable domain policies, maintainable change units.

---

## Practice prompt
Design a `PricingService` where discount rules are pluggable per market without changing checkout orchestration.

```java
interface DiscountPolicy {
    long apply(long subtotalCents, CustomerContext ctx);
}

final class CheckoutOrchestrator {
    private final DiscountPolicy discountPolicy;

    CheckoutOrchestrator(DiscountPolicy discountPolicy) {
        this.discountPolicy = discountPolicy;
    }

    long finalTotal(long subtotalCents, CustomerContext ctx) {
        return discountPolicy.apply(subtotalCents, ctx);
    }
}

record CustomerContext(String segment, String country) {}
```
