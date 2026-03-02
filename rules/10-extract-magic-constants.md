---
summary: "Checks that magic numbers and string literals are extracted into named constants"
file_patterns: [["*.dart"], ["*.java"], ["*.py"]]
tags: ["mikeh", "readability"]
sources: ["D54911", "D86985", "D59374"]
---
# Extract Magic Constants

If this rule is not relevant to the code under review, state so and move on.

Replace magic numbers and repeated string literals with named constants. A bare `100` or `"/api/v1/modules"` in code forces readers to guess the meaning. Named constants make limits discoverable, reviewable, and changeable from a single location.

Mike's principles: *"arbitrary — lets make constant at least"*, *"probably want a bunch of constants for all of these at the top of the file?"*

- Flag numeric literals (other than 0, 1, -1) used in conditions, limits, or configuration.
- Flag repeated string literals that represent API paths, keys, or configuration values.
- Constants should have descriptive names that explain the value's purpose.

## Example

Bad:
```java
if (cells.size() > 100) {
    throw new TooManyCellsException();
}
String url = baseUrl + "/api/v1/modules";
```

Good:
```java
private static final int MAX_CELLS_PER_SUBDIVIDE = 100;
private static final String MODULES_API_PATH = "/api/v1/modules";

if (cells.size() > MAX_CELLS_PER_SUBDIVIDE) {
    throw new TooManyCellsException();
}
String url = baseUrl + MODULES_API_PATH;
```
