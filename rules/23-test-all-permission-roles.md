---
summary: "Checks that permission and access-control logic is tested with every relevant user role"
file_patterns: [["*_test.dart"], ["*_test.py"], ["*Test.java"]]
tags: ["mikeh", "testing", "security"]
sources: ["D70026", "D67232"]
---
# Test All Permission Roles

If this rule is not relevant to the code under review, state so and move on.

When code implements access control or permission checks, tests must exercise every relevant user role — not just the happy path. Mike meticulously reviews permission logic, insisting on exhaustive coverage: admin, regular user, read-only user, unauthenticated, and any custom roles. Missing a role in tests means missing a potential security hole.

- Flag permission-related tests that only check one role (usually admin or the "allowed" case).
- Ensure both "allowed" and "denied" paths are tested for each role.
- Flag ambiguous permission naming that could lead to incorrect access decisions.

## Example

Bad:
```dart
test('admin can delete project', () {
  final user = createUser(role: Role.admin);
  expect(service.deleteProject(user, projectId), completes);
});
// No tests for other roles — do viewers get blocked?
```

Good:
```dart
test('admin can delete project', () {
  final user = createUser(role: Role.admin);
  expect(service.deleteProject(user, projectId), completes);
});
test('viewer cannot delete project', () {
  final user = createUser(role: Role.viewer);
  expect(() => service.deleteProject(user, projectId), throwsA(isA<PermissionDeniedException>()));
});
test('unauthenticated user cannot delete project', () {
  expect(() => service.deleteProject(null, projectId), throwsA(isA<AuthenticationRequiredException>()));
});
```
