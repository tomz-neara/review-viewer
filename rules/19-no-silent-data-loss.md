---
summary: "Checks that changes do not silently lose, corrupt, or ignore existing data"
file_patterns: [["*"]]
tags: ["mikeh", "correctness", "data-safety"]
sources: ["D56510", "D81681", "D70026"]
---
# No Silent Data Loss

If this rule is not relevant to the code under review, state so and move on.

Changes must not silently drop, corrupt, or ignore existing data. When modifying serialization formats, database schemas, or data pipelines, ensure old data is either migrated or explicitly handled. Mike flags changes that could cause silent data loss — especially during version upgrades or format changes.

- Flag schema or format changes that don't handle pre-existing data.
- Flag code that silently ignores unknown fields or values instead of logging/warning.
- Flag filter or transformation logic that could discard valid data without notice.
- Ensure migration paths exist for breaking changes.

## Example

Bad:
```dart
// New enum value added, but deserialization ignores unknown values silently
Status parseStatus(String s) {
  return Status.values.firstWhere(
    (v) => v.name == s,
    orElse: () => Status.unknown,  // old data silently becomes "unknown"
  );
}
```

Good:
```dart
Status parseStatus(String s) {
  final match = Status.values.where((v) => v.name == s);
  if (match.isEmpty) {
    log.warning('Unknown status "$s" — defaulting to unknown. '
        'Consider adding a migration.');
    return Status.unknown;
  }
  return match.first;
}
```
