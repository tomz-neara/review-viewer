---
summary: "Checks that boolean and numeric literal arguments use named parameters for clarity"
file_patterns: [["*.dart"]]
tags: ["mikeh", "dart", "readability"]
sources: ["D81681", "D72158", "D76209"]
---
# Use Named Parameters for Boolean and Numeric Literals

If this rule is not relevant to the code under review, state so and move on.

When calling functions with boolean or numeric literals, use named parameters so the call site is self-documenting. Bare `true`, `false`, or numeric values at call sites force readers to look up the function signature. Dart has first-class named parameter support — use it.

Mike's principle: *"named arg for bool is easier to read"*

- Flag calls passing bare `true`/`false` or ambiguous numeric literals as positional arguments.
- Named parameters make the intent clear without consulting the callee.
- This is especially important when multiple booleans or numbers appear together.

## Example

Bad:
```dart
fetchData(true, false, 500);
buildWidget(true, true);
```

Good:
```dart
fetchData(useCache: true, forceRefresh: false, timeoutMs: 500);
buildWidget(showHeader: true, isScrollable: true);
```
