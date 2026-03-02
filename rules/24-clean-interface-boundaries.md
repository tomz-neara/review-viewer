---
summary: "Checks that public APIs don't leak implementation details across layer boundaries"
file_patterns: [["*.dart"], ["*.java"]]
tags: ["mikeh", "architecture", "maintainability"]
sources: ["D81681", "D67232", "D54911"]
---
# Don't Leak Implementation Details Across Layers

If this rule is not relevant to the code under review, state so and move on.

Classes should expose clean interfaces that hide complexity. Don't leak implementation details (internal data structures, serialization formats, database schemas) across layer boundaries. Mike pushes for separation of concerns — client/server, data/presentation, domain/infrastructure — and flags code that couples layers unnecessarily.

- Flag public APIs that expose internal implementation types (e.g., returning a raw database row or protobuf object).
- Flag classes that directly depend on another layer's internals instead of going through an interface.
- Prefer dependency injection over singletons for testability.

## Example

Bad:
```dart
// Leaks database implementation into the domain layer
class ProjectService {
  final Database _db;
  Map<String, dynamic> getProject(String id) {
    return _db.query('SELECT * FROM projects WHERE id = ?', [id]);
  }
}
```

Good:
```dart
class ProjectService {
  final ProjectRepository _repo;
  Project getProject(String id) {
    return _repo.findById(id);  // returns domain object, not raw DB row
  }
}
```
