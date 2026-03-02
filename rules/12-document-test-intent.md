---
summary: "Checks that test functions have descriptions explaining what behavior they verify and why"
file_patterns: [["*_test.dart"], ["*_test.py"], ["*Test.java"]]
tags: ["mikeh", "testing", "documentation"]
sources: ["D56510", "D67232", "D65292"]
---
# Document Test Intent

If this rule is not relevant to the code under review, state so and move on.

Test functions should document what behavior they verify and why the test exists. If a test was written for a specific bug, label it as a regression test. After refactoring, review whether existing tests are still meaningful — if the test's purpose is unclear, it should be documented or removed.

Mike's principles: *"Please document the intent behind the test setup"*, *"Please document these tests properly, if they are still needed. I guess they caught a bug in the deleted implementation? Can call them regression tests."*

- Flag test names that are generic numbers or don't describe behavior (e.g. `test3`, `testAsync`).
- Flag test functions that lack a doc comment explaining the scenario and expected behavior.
- Regression tests should reference the bug or diff they guard against.

## Example

Bad:
```dart
test('async data test 3', () {
  // ... 50 lines of setup and assertions ...
});
```

Good:
```dart
test('pollState returns stale when dependency invalidated during async execution (regression for D67232)', () {
  // Verifies that invalidating a dependency mid-poll correctly
  // transitions the state to stale rather than silently succeeding.
  // ... setup and assertions ...
});
```
