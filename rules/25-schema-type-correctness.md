---
summary: "Checks that schema fields use correct nullability — nullable only when absence is semantically meaningful"
file_patterns: [["*.dart"], ["*.java"], ["*.py"]]
tags: ["mikeh", "correctness", "schema"]
sources: ["D81681", "D56510", "D67232"]
---
# Schema Nullability Correctness

If this rule is not relevant to the code under review, state so and move on.

Schema fields (ADL, protobuf, JSON, database) should be nullable only when absence is semantically meaningful. Non-nullable fields that get null-checked in code are misleading; nullable fields that should always have a value invite bugs. Mike flags mismatches between schema nullability and actual usage.

- Flag fields marked nullable that should always have a value.
- Flag fields marked non-nullable where code checks for null — the schema and code disagree.
- Flag generated code that doesn't reflect the intended schema semantics.

## Example

Bad:
```
// Schema: non-nullable, but code checks for null
struct Session {
  Double startedAt;  // non-nullable in schema
}

// Code:
if (session.startedAt == null) { ... }  // impossible per schema, misleading
```

Good:
```
// Schema: nullable because session may not have started yet
struct Session {
  Nullable<Double> startedAtEpochMs;  // nullable = session not yet started
}

// Code:
if (session.startedAtEpochMs == null) {
  // Session hasn't started yet — this is a valid state
}
```
