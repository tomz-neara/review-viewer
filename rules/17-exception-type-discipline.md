---
summary: "Checks that catch blocks use specific exception types and don't swallow errors"
file_patterns: [["*.dart"], ["*.java"], ["*.py"]]
tags: ["mikeh", "correctness", "error-handling"]
sources: ["D81681", "D67232", "D86985"]
---
# Catch Specific Exception Types

If this rule is not relevant to the code under review, state so and move on.

Distinguish between recoverable exceptions (`Exception`) and programming errors (`Error`). Catch specific exception types rather than bare `catch` or `catch (Exception)`. Mike flags sloppy exception handling that masks bugs or hides useful diagnostic information.

- Flag bare `catch` or overly broad `catch (Exception)` that swallows unexpected errors.
- Flag code that catches `Error` subtypes — these represent bugs and should not be caught.
- Flag empty catch blocks that silently discard errors without logging.
- Ensure error messages include enough context to diagnose the issue.

## Example

Bad:
```dart
try {
  await conn.execute(query);
} catch (e) {
  // swallows everything including programming errors
}
```

Good:
```dart
try {
  await conn.execute(query);
} on DatabaseException catch (e) {
  log.warning('Query failed on table $tableName: $e');
  rethrow;
}
```
