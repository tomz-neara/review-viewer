---
summary: "Checks that print statements and debug logging are removed or replaced with proper logging before merging"
file_patterns: [["*.dart"], ["*.java"], ["*.py"], ["*.cpp"]]
tags: ["mikeh", "cleanup"]
sources: ["D86985", "D56510", "D87014"]
---
# Remove Debug Print Statements

If this rule is not relevant to the code under review, state so and move on.

Remove `print` statements, `console.log`, `debugPrint`, and ad-hoc debug-only logging before merging. Use a proper logging framework instead. Mike insists on production-ready code — debug output should not ship.

- Flag `print()`, `debugPrint()`, or `console.log()` calls that are not part of intentional structured logging.
- Flag logging that includes "DEBUG", "TEST", or "TEMP" in the message — these are debug artifacts.
- Replace with proper logging: `_log.fine()`, `_log.warning()`, or equivalent framework calls.

## Example

Bad:
```dart
void processModule(Module m) {
  print('DEBUG: processing ${m.id}');  // leftover debug
  return transform(m);
}
```

Good:
```dart
void processModule(Module m) {
  _log.fine('Processing module ${m.id}');  // proper logging
  return transform(m);
}
```
