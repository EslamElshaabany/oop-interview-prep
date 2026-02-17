# 04 - Interface vs Abstract Class

This decision affects extensibility, compatibility, and implementation clarity.

## Distilled difference
- **Interface**: behavior contract; favors pluggability and multiple implementations.
- **Abstract class**: shared mechanics/state; favors controlled extension with reuse.

---

## Interface-first for integration points

```java
interface RetryPolicy {
    boolean shouldRetry(int attempt, Throwable error);
}

final class ExponentialBackoffRetryPolicy implements RetryPolicy {
    @Override
    public boolean shouldRetry(int attempt, Throwable error) {
        return attempt < 5;
    }
}
```

Use when different modules need interchangeable implementations.

---

## Abstract class for skeletal implementation

```java
abstract class AbstractBatchProcessor<T> {
    public final void processBatch(Iterable<T> items) {
        before();
        for (T item : items) processOne(item);
        after();
    }

    protected void before() {}
    protected abstract void processOne(T item);
    protected void after() {}
}

final class UserBatchProcessor extends AbstractBatchProcessor<String> {
    @Override
    protected void processOne(String userId) {
        // actual processing
    }
}
```

Use when extension points are known and base workflow should be enforced.

---

## Trade-offs to keep in mind
- Interface + composition often beats inheritance for long-term flexibility.
- Abstract classes are useful for framework internals and reusable workflows.
- Default methods in interfaces help evolution, but overuse can blur contracts.
- Ask: do we need shared state/implementation or only a behavior contract?

## Rule of thumb
Start with interface + composition. Introduce abstract base classes only when duplication and lifecycle coupling justify it.
