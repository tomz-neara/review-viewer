---
summary: "Checks that repeated operations are batched and expensive computations are not needlessly redone"
file_patterns: [["*"]]
tags: ["mikeh", "performance"]
sources: ["D56510", "D81681", "D70026"]
---
# Avoid Redundant Work and N+1 Operations

If this rule is not relevant to the code under review, state so and move on.

When processing collections, avoid patterns that issue one operation per item when a single batch operation would suffice. Similarly, avoid recomputing expensive values inside loops when they could be computed once outside. Mike flags unnecessary work that degrades performance and makes code harder to reason about.

- Flag loops that make network calls, database queries, or file reads per element (N+1 pattern).
- Flag expensive computations repeated inside loops that could be hoisted.
- Suggest batching, caching, or memoization where appropriate.
- Flag redundant re-traversals of the same collection.

## Example

Bad:
```dart
for (final id in moduleIds) {
  final module = await fetchModule(id);  // N+1: one request per module
  results.add(module);
}
```

Good:
```dart
final modules = await fetchModules(moduleIds);  // single batch request
results.addAll(modules);
```
