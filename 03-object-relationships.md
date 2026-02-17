# 03 - Object Relationships and Ownership

Relationship choice is mostly about **ownership**, **lifecycle**, and **coupling direction**.

## Quick matrix
- **Association**: objects collaborate; no ownership implied.
- **Aggregation**: whole-part with weak ownership (parts can outlive whole).
- **Composition**: strong ownership (part lifecycle bound to whole).

---

## 1) Association

```java
class FraudService {
    boolean isRisky(String userId) { return false; }
}

class CheckoutService {
    private final FraudService fraudService; // collaboration only

    CheckoutService(FraudService fraudService) {
        this.fraudService = fraudService;
    }
}
```

No lifecycle ownership; this is dependency/collaboration.

---

## 2) Aggregation

```java
import java.util.List;

class Engineer {
    private final String id;
    Engineer(String id) { this.id = id; }
}

class Squad {
    private final List<Engineer> members; // members exist independently

    Squad(List<Engineer> members) {
        this.members = members;
    }
}
```

If `Squad` disappears, `Engineer` objects still make sense.

---

## 3) Composition

```java
import java.util.ArrayList;
import java.util.List;

class Order {
    private final List<OrderLine> lines = new ArrayList<>(); // strongly owned

    void addLine(String sku, int qty) {
        lines.add(new OrderLine(sku, qty));
    }

    private static class OrderLine {
        private final String sku;
        private final int qty;

        private OrderLine(String sku, int qty) {
            this.sku = sku;
            this.qty = qty;
        }
    }
}
```

`OrderLine` has no valid standalone lifecycle outside `Order`.

---

## Discussion points
- Start from lifecycle rules in the domain.
- Prefer composition for strict invariant control.
- Prefer loose association for infrastructure dependencies.
- Keep dependency direction from high-level policy to abstractions.

## Common anti-pattern
Using inheritance to model relationships that are really composition/association.
