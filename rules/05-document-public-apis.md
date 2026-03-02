---
summary: "Checks that new or modified public classes, methods, and fields have documentation"
file_patterns: [["*.dart"], ["*.java"]]
tags: ["mikeh", "documentation"]
sources: ["D81681", "D70026", "D86985", "D67232"]
---
# Document Public APIs

If this rule is not relevant to the code under review, state so and move on.

Every new or modified public class, method, and non-trivial field should have a doc comment explaining its purpose, not just restating the name. Documentation should cover intent, assumptions, and edge cases. Outdated docs are worse than no docs — update them when behavior changes.

Mike's principle: He flags undocumented APIs with single-word *"doc"* comments repeatedly across nearly every review. *"would like clarity on this / doc this"*, *"ensure doc consistent with interface or move there?"*

- Flag public classes, methods, and fields that lack doc comments.
- Doc comments should explain WHY, not just restate the name.
- When modifying behavior, verify existing docs still match.

## Example

Bad:
```dart
void processData(List<int> input) { ... }

class ProjectionManager { ... }
```

Good:
```dart
/// Deduplicates and sorts input points by timestamp, discarding entries
/// with ts=0 that represent uninitialized sensors.
void processData(List<int> input) { ... }

/// Manages lifecycle of coordinate projections, caching computed
/// transformations and invalidating when the source CRS changes.
class ProjectionManager { ... }
```
