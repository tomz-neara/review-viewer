---
summary: "Checks that refactors and renames audit all call-sites for regressions"
file_patterns: [["*"]]
tags: ["mikeh", "correctness", "refactoring"]
sources: ["D56510", "D67232", "D70026"]
---
# Verify All Call-Sites During Refactors

If this rule is not relevant to the code under review, state so and move on.

When renaming, moving, or changing the signature of a function, class, or schema field, all call-sites must be audited. Mike requires careful verification that refactors don't introduce regressions — especially when the change spans multiple files or layers (client/server, schema/code).

- Flag renames or signature changes where not all callers are updated in the same diff.
- Flag schema renames that don't update generated code consumers.
- Flag removed methods/fields where callers may exist outside the changed files.
- If a refactor changes behavior (not just names), ensure tests cover the new behavior.

## Example

Bad:
```dart
// Renamed method but only updated 2 of 5 callers
class ProjectionService {
  // Was: computeTransform() → now: applyProjection()
  void applyProjection() { ... }
}
// Other files still call computeTransform() → compile error or silent breakage
```

Good:
```dart
// All callers updated, and a test verifies the new behavior
class ProjectionService {
  void applyProjection() { ... }
}
// grep -r 'computeTransform' shows 0 remaining references
// Test: testApplyProjection() verifies the renamed method works identically
```
