---
summary: "Checks that lambda parameters and non-obvious local variables have explicit type annotations"
file_patterns: [["*.dart"]]
tags: ["mikeh", "dart", "readability"]
sources: ["D86985", "D76209", "D56510"]
---
# Specify Explicit Types in Lambdas and Non-Obvious Locals

If this rule is not relevant to the code under review, state so and move on.

Specify explicit types on lambda parameters and local variables when the type is not immediately obvious from the right-hand side. With imperfect IDE support, types visible in code prevent misinterpretation and make diffs reviewable without an IDE.

Mike's principles: *"specify types in lambdas"*, *"please put back `PointWithClass`... it's helpful to have the types in plain sight"*

- Lambda parameters should have explicit type annotations unless the type is trivially obvious (e.g. `int`, `String` from a `List<int>`).
- Local variables initialized from method calls or complex expressions should have explicit types.
- If a reviewer can't determine the type at a glance, annotate it.

## Example

Bad:
```dart
list.map((e) => e.transform());
final result = computeProjection();
```

Good:
```dart
list.map((ProjectorHandle e) => e.transform());
final ProjectionResult result = computeProjection();
```
