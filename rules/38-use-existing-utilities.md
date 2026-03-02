---
summary: "Checks that code uses existing library and codebase utilities instead of reimplementing common operations"
file_patterns: [["*.java"]]
tags: ["petro", "java", "reuse", "maintainability"]
sources: ["D88138", "D50996", "D63362", "D56979", "D50236"]
---
# Use Existing Utilities

If this rule is not relevant to the code under review, state so and move on.

Petro frequently points out that a helper method, library function, or framework capability already exists for what the author wrote by hand. Reimplementing logic that lives in Guava, the JDK standard library, or the project's own utility classes creates divergence, misses edge-case handling the existing code already covers, and adds maintenance burden.

- Flag hand-rolled null-or-empty checks when `Strings.isNullOrEmpty`, `Objects.requireNonNull`, or `Optional` would suffice.
- Flag manual collection transformations (loops that map/filter/collect) when a `Stream` one-liner or Guava utility (`Lists.transform`, `Maps.uniqueIndex`) does the same thing.
- Flag custom assertion logic in tests when the project provides dedicated assertion helpers (e.g., `ExceptionAsserts`, custom Hamcrest matchers).
- Flag reimplemented patterns (retry loops, batching, try-with-resources wrappers) when the codebase already has a shared utility for them.
- New utilities are fine when no existing one fits — flag reimplementing one that already exists.

## Example

Bad:
```java
String value = map.get(key);
if (value == null) {
    value = computeDefault(key);
    map.put(key, value);
}

// manual list transformation
List<String> names = new ArrayList<>();
for (User user : users) {
    names.add(user.getName());
}
```

Good:
```java
String value = map.computeIfAbsent(key, this::computeDefault);

// stream or Guava utility
List<String> names = users.stream()
    .map(User::getName)
    .toList();
```
