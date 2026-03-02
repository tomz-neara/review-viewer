---
summary: "Checks that predicates, map operations, and other pure functional operations do not perform side effects"
file_patterns: [["*.dart"], ["*.java"]]
tags: ["mikeh", "correctness", "functional"]
sources: ["D59394", "D83700", "D50803", "D83976"]
---
# No Side Effects in Pure Operations

If this rule is not relevant to the code under review, state so and move on.

Predicates, map functions, and filter callbacks should be pure — they compute a value and return it without modifying external state. Mike flags code that sneaks side effects (adding to a list, updating a counter, logging, sending events) inside functional operations like `map`, `filter`, `where`, or `any`. These hidden mutations make the code harder to reason about, break when the stream is lazily evaluated, and obscure the actual data flow.

- Flag `map`, `filter`, `where`, `any`, or `every` callbacks that mutate external state (add to a collection, increment a counter, fire an event).
- Flag predicates passed to `removeWhere`, `firstWhere`, or `singleWhere` that perform writes or logging as a side effect.
- If you need both a transformation and a side effect, use an explicit `for` loop so the mutation is visible.
- Logging at debug level inside a map is tolerable if it has no behavioral impact; flag anything that changes state.

## Example

Bad:
```java
List<String> errors = new ArrayList<>();
items.stream()
    .filter(item -> {
        if (!item.isValid()) {
            errors.add(item.getName());  // side effect hidden in filter
            return false;
        }
        return true;
    })
    .map(Item::process)
    .toList();
```

Good:
```java
// Separate the concerns: partition first, then process
Map<Boolean, List<Item>> partitioned = items.stream()
    .collect(Collectors.partitioningBy(Item::isValid));

List<String> errors = partitioned.get(false).stream()
    .map(Item::getName)
    .toList();

List<Result> results = partitioned.get(true).stream()
    .map(Item::process)
    .toList();
```
