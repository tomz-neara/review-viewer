---
summary: "Checks that APIs and data structures make invalid states unrepresentable and use safe defaults"
file_patterns: [["*.dart"], ["*.java"]]
tags: ["mikeh", "correctness", "defensive-coding"]
sources: ["D63639", "D87663", "D68063", "D74756", "D72056", "D73713", "D78877"]
---
# Prefer Defensive Construction

If this rule is not relevant to the code under review, state so and move on.

The best validation is the kind you never need to write because the type system or constructor prevents the bad state from existing. Mike flags code that relies on runtime checks or documentation to enforce invariants when the design could make the wrong thing impossible to express. A constructor that rejects duplicates is better than a downstream check; a required parameter is better than a nullable one with a "must not be null" comment.

- Flag constructors or factory methods that accept obviously invalid combinations of parameters (e.g., both `startDate` and `endDate` where start > end is silently allowed) — validate at construction or use a type that enforces the constraint.
- Flag default values of `0`, `""`, or `null` for fields where those values are never semantically valid — prefer requiring the caller to supply a meaningful value or choose a safe sentinel that cannot collide with real data.
- Flag collections built by accumulation that should enforce uniqueness — use a `Set` or `Map` at construction rather than a `List` with a post-hoc duplicate check.
- Flag APIs that expose raw mutable state (returning a mutable list or map field) when callers should not modify it — return an unmodifiable view or a copy.
- Designing out the bad state is preferred over documenting it; documenting it is preferred over silently allowing it.

## Example

Bad:
```java
// Caller can construct an invalid range — nothing prevents start > end
public class DateRange {
    public final LocalDate start;
    public final LocalDate end;

    public DateRange(LocalDate start, LocalDate end) {
        this.start = start;
        this.end = end;
    }
}
```

Good:
```java
// Invalid range is impossible — rejected at construction
public class DateRange {
    public final LocalDate start;
    public final LocalDate end;

    private DateRange(LocalDate start, LocalDate end) {
        this.start = start;
        this.end = end;
    }

    public static DateRange of(LocalDate start, LocalDate end) {
        Preconditions.checkArgument(!start.isAfter(end),
            "start %s must not be after end %s", start, end);
        return new DateRange(start, end);
    }
}
```
