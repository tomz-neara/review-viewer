---
summary: "Checks that multi-argument calls use trailing commas and one-argument-per-line formatting in Dart"
file_patterns: [["*.dart"]]
tags: ["mikeh", "dart", "formatting"]
sources: ["D86985", "D81681"]
---
# Use Trailing Commas and One-Arg-Per-Line in Dart

If this rule is not relevant to the code under review, state so and move on.

In Dart, use trailing commas in multi-argument function calls and collection literals. This triggers `dart format` to place each argument on its own line, producing cleaner diffs and more readable code. Mike flags formatting noise and IDE misconfiguration that produces inconsistent formatting.

- Flag multi-argument calls that span multiple lines but lack a trailing comma.
- Flag widget constructors with 3+ arguments crammed on one line.
- Trailing commas produce cleaner git diffs (adding/removing one arg = one line changed).

## Example

Bad:
```dart
final widget = Container(color: Colors.red, padding: EdgeInsets.all(8),
    child: Text('hello'));
```

Good:
```dart
final widget = Container(
  color: Colors.red,
  padding: EdgeInsets.all(8),
  child: Text('hello'),
);
```
