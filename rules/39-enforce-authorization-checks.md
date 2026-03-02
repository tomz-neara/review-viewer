---
summary: "Checks that endpoints and sensitive operations enforce authorization at point of access"
file_patterns: [["*.dart"], ["*.java"], ["*.py"]]
tags: ["mikeh", "security", "access-control"]
sources: ["D65742", "D51677", "D53916", "D76498", "D84746"]
---
# Enforce Authorization Checks

If this rule is not relevant to the code under review, state so and move on.

Every endpoint and sensitive operation must verify that the caller has the required role or permission before executing. Mike scrutinizes access control logic to ensure checks happen at the point of access — not just at creation time — and that no role (external user, org admin, super admin) can reach operations they shouldn't. This complements rule 23 (test all permission roles), which covers the testing side; this rule covers the implementation side.

- Flag API endpoints or service methods that perform sensitive operations (writes, deletes, sharing, admin actions) without an explicit authorization check.
- Flag permission checks that gate on creation but not on subsequent access — a user's role can change between creation and access.
- Flag features guarded by URL flags or client-side checks instead of server-side permission verification.
- Flag broad permission grants (e.g., allowing all authenticated users) when the operation should be restricted to specific roles.
- Internal service-to-service calls with established trust boundaries do not need redundant checks.

## Example

Bad:
```java
@PostMapping("/api/projects/{id}/delete")
public void deleteProject(@PathVariable String id) {
    // no authorization check — any authenticated user can delete
    projectService.delete(id);
}
```

Good:
```java
@PostMapping("/api/projects/{id}/delete")
public void deleteProject(@PathVariable String id, @AuthUser User user) {
    if (!permissionService.canDelete(user, id)) {
        throw new ForbiddenException("User lacks delete permission for project " + id);
    }
    projectService.delete(id);
}
```
