# 08 - Design Exercise: Pricing Engine

## Problem statement
Design a checkout pricing engine that supports:
- base subtotal
- market-specific tax policy
- multiple discount strategies
- extensible promotion rules without touching core checkout flow

This is a policy-rich domain with rules that evolve over time.

## Proposed object model
- `PricingService` orchestrates pricing flow.
- `TaxPolicy` and `DiscountPolicy` are pluggable abstractions.
- `PricingContext` carries relevant request metadata.
- `Money` value object protects currency arithmetic boundaries.

```java
import java.math.BigDecimal;

record Money(BigDecimal amount, String currency) {
    Money {
        if (amount.signum() < 0) throw new IllegalArgumentException("negative money");
    }

    Money plus(Money other) {
        if (!currency.equals(other.currency)) throw new IllegalArgumentException("currency mismatch");
        return new Money(amount.add(other.amount), currency);
    }

    Money minus(Money other) {
        if (!currency.equals(other.currency)) throw new IllegalArgumentException("currency mismatch");
        return new Money(amount.subtract(other.amount), currency);
    }
}

record PricingContext(String country, String customerTier) {}

interface TaxPolicy {
    Money taxFor(Money subtotal, PricingContext ctx);
}

interface DiscountPolicy {
    Money discountFor(Money subtotal, PricingContext ctx);
}

final class PricingService {
    private final TaxPolicy taxPolicy;
    private final DiscountPolicy discountPolicy;

    PricingService(TaxPolicy taxPolicy, DiscountPolicy discountPolicy) {
        this.taxPolicy = taxPolicy;
        this.discountPolicy = discountPolicy;
    }

    Money finalPrice(Money subtotal, PricingContext ctx) {
        Money discount = discountPolicy.discountFor(subtotal, ctx);
        Money taxedBase = subtotal.minus(discount);
        Money tax = taxPolicy.taxFor(taxedBase, ctx);
        return taxedBase.plus(tax);
    }
}
```

## Extensions to discuss
1. **Policy registry:** select policy by country/tenant at runtime.
2. **Observability:** emit structured pricing decision logs.
3. **Determinism:** guarantee same input yields same output for auditability.
4. **Versioning:** support old/new pricing rules during rollout.
5. **Testing:** contract tests for each policy + orchestration tests for flow.

## What to evaluate
- Domain decomposition quality
- Boundary clarity and extensibility
- Handling of change without regression risk
- Ability to justify trade-offs (simplicity vs flexibility)
