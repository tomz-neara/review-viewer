---
summary: "Checks that unused variables, unreachable code, and untested dead paths are removed"
file_patterns: [["*"]]
tags: ["mikeh", "cleanup"]
sources: ["D56510", "D87014", "D70026"]
---
# Remove Dead Code

If this rule is not relevant to the code under review, state so and move on.

Remove unused variables, dead imports, unreachable branches, and code that exists "for later" but has no tests or callers. Unused code rots — it misleads readers and drifts from the intended behavior. If something might be needed later, delete it and leave a TODO comment or tracking ticket.

Mike's principle: *"if this is unused code, it means it is also untested. I would rather not have it, and add it if/when it is needed."*

- Flag functions, methods, or variables that are added but never called or referenced.
- Flag commented-out code blocks that are checked in.
- Speculative code without tests is a liability, not an asset.

## Example

Bad:
```dart
// Added for future use
List<int> mergeTimestamps(List<int> a, List<int> b) {
  return [...a, ...b]..sort();
}
```

Good:
```dart
// TODO(mikeh): Add timestamp merging when needed (see TICKET-123).
// Removed unused mergeTimestamps() — will re-add with tests when required.
```
