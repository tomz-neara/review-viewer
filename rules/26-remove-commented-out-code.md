---
summary: "Checks that commented-out code and bare TODO/FIXME markers without ticket references are removed"
file_patterns: [["*.dart"], ["*.java"], ["*.py"], ["*.cpp"]]
tags: ["mikeh", "cleanup"]
sources: ["D86985", "D56510", "D87014"]
---
# Remove Commented-Out Code and Bare TODOs

If this rule is not relevant to the code under review, state so and move on.

Don't check in commented-out code blocks — use version control to preserve history. Bare `TODO` or `FIXME` comments without a ticket reference are noise that never gets resolved. Mike insists that code should be clean and production-ready before merging.

- Flag commented-out code blocks (more than a single line of commented code).
- Flag bare `TODO` or `FIXME` without a ticket or author reference.
- TODOs with references are acceptable: `// TODO(mikeh): Handle X (TICKET-456)`

## Example

Bad:
```dart
// TODO: fix this later
final result = transform(m);
// final oldResult = legacyTransform(m);
// if (oldResult != result) { log.warning('mismatch'); }
```

Good:
```dart
// TODO(mikeh): Handle edge case for empty modules (TICKET-456)
final result = transform(m);
```
