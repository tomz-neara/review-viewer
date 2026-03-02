---
summary: "Checks that public APIs, service endpoints, and persistence writes validate required parameters"
file_patterns: [["*.dart"], ["*.java"], ["*.py"]]
tags: ["eric", "correctness", "validation"]
sources: ["D76523", "D83296", "D63552"]
---
# Validate Inputs at Boundaries

If this rule is not relevant to the code under review, state so and move on.

Public APIs, service endpoints, and persistence writes are system boundaries where invalid data enters the codebase. Eric insists that these boundaries validate required parameters, enforce constraints, and reject bad data early — before it propagates into internal logic where failures are harder to diagnose and recover from.

- Flag public API methods or service endpoints that pass raw user input to internal logic without validation.
- Flag persistence writes (database inserts/updates) that do not validate required fields before executing.
- Flag missing null/empty checks on required parameters at public method boundaries.
- Internal helper methods called only by already-validated code do not need redundant checks.
- Validation errors should include which parameter failed and why.

## Example

Bad:
```dart
Future<void> createUser(Map<String, dynamic> request) async {
  // raw input passed straight to DB — no validation
  await db.insert('users', {
    'name': request['name'],
    'email': request['email'],
    'role': request['role'],
  });
}
```

Good:
```dart
Future<void> createUser(Map<String, dynamic> request) async {
  final name = request['name'] as String?;
  final email = request['email'] as String?;
  if (name == null || name.isEmpty) {
    throw ArgumentError('name is required');
  }
  if (email == null || !email.contains('@')) {
    throw ArgumentError('valid email is required');
  }
  final role = request['role'] as String? ?? 'viewer';

  await db.insert('users', {
    'name': name,
    'email': email,
    'role': role,
  });
}
```
