---
summary: "Checks for unnecessary null assertions, missing Optional usage, and overly nullable declarations"
file_patterns: [["*.dart"], ["*.java"]]
tags: ["eric", "correctness", "type-safety"]
sources: ["D73926", "D71667", "D63639"]
---
# Null-Safety Discipline

If this rule is not relevant to the code under review, state so and move on.

The type system can prevent null-related crashes — but only if nullability annotations are honest. Eric flags code that undermines null safety through unnecessary bang operators, nullable declarations for always-initialized variables, and Java methods that return `null` instead of `Optional`. The goal is to let the compiler catch null bugs at compile time instead of discovering them at runtime.

- Flag Dart `!` (bang operator) where the value can be proven non-null by control flow or where a null check with an early return is safer.
- Flag variables declared as nullable (`String?`) when they are always initialized before use — tighten the type to non-nullable.
- Flag Java methods that return `null` to indicate absence — return `Optional<T>` instead.
- Flag nullable parameters in constructors where every call site passes a non-null value — make the parameter required and non-nullable.
- This complements rule 25 (schema nullability) which covers database schemas; this rule covers application code.

## Example

Bad:
```dart
class UserProfile {
  String? name;  // always set in constructor, should not be nullable

  UserProfile(Map<String, dynamic> data) {
    name = data['name'] as String;
  }

  String displayName() {
    return name!.toUpperCase();  // bang operator hides the real issue
  }
}
```

Good:
```dart
class UserProfile {
  final String name;  // non-nullable, set once

  UserProfile(Map<String, dynamic> data)
    : name = data['name'] as String;

  String displayName() {
    return name.toUpperCase();  // no bang needed
  }
}
```
