---
summary: "Checks that names are precise and unambiguous, avoiding generic terms when specific ones exist"
file_patterns: [["*"]]
tags: ["mikeh", "naming", "readability"]
sources: ["D81681", "D54911", "D67232", "D86985"]
---
# Use Precise, Unambiguous Names

If this rule is not relevant to the code under review, state so and move on.

Names should precisely convey purpose. Avoid generic names like `data`, `result`, `process`, `handle` when a specific term exists. Boolean variables should read as questions (`isRunning`, `hasTimestamp`). Method names should describe the action precisely. Ultra clarity is more important than brevity or theoretical generality.

Mike's principles: *"rename to `vectorFieldProjector` ... ultra clarity is more important than 'generic in theory but not in practice'"*, *"rename to `isCurrentlyRunning`"*, *"`subdivideCellNoInsert` is bad name - just `subdivide`"*

- Flag variables named `data`, `result`, `temp`, `val`, `item` when a domain-specific name exists.
- Flag boolean variables that don't read as yes/no questions.
- Flag names that include unnecessary implementation details or negations.
- If a class/method only handles one specific thing, name it for that thing.

## Example

Bad:
```dart
class _ProjectionKey { ... }  // it's a mix of inputs and outputs
bool running;                  // is it running? was it running?
void handleData(var result) { ... }
```

Good:
```dart
class _ProjectorHandle { ... }
bool isCurrentlyRunning;
void applyCoordinateTransform(ProjectionResult projection) { ... }
```
