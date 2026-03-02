---
summary: "Checks that deeply nested conditionals are flattened using guard clauses and early returns"
file_patterns: [["*"]]
tags: ["petro", "readability", "control-flow"]
sources: ["D76216", "D50996", "D63416"]
---
# Prefer Early Returns

If this rule is not relevant to the code under review, state so and move on.

Deep nesting makes control flow hard to follow. When a method has three or more levels of nested conditionals, it can almost always be simplified with guard clauses that return, throw, or continue early. Petro flags the "arrow anti-pattern" — code where the happy path is buried inside deeply nested blocks instead of being the main body of the method.

- Flag methods with 3+ levels of nested `if`/`else` blocks that could be flattened with guard clauses.
- Flag the happy path buried inside nested conditionals — the main logic should be at the top level of the method.
- Flag `else` blocks that follow a block ending in `return`, `throw`, or `continue` — the `else` is unnecessary.
- Guard clauses should handle error/edge cases first, leaving the happy path un-indented.

## Example

Bad:
```dart
String processItem(Item? item) {
  if (item != null) {
    if (item.isValid) {
      if (item.hasPermission) {
        return doWork(item);
      } else {
        return 'no permission';
      }
    } else {
      return 'invalid';
    }
  } else {
    return 'missing';
  }
}
```

Good:
```dart
String processItem(Item? item) {
  if (item == null) {
    return 'missing';
  }
  if (!item.isValid) {
    return 'invalid';
  }
  if (!item.hasPermission) {
    return 'no permission';
  }

  return doWork(item);
}
```
