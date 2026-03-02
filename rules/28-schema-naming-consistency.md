---
summary: "Checks that schema field names are consistent across schema definitions, generated code, and application code"
file_patterns: [["*_schema.dart"], ["*.adl"]]
tags: ["mikeh", "correctness", "schema"]
sources: ["D81681", "D56510", "D67232"]
---
# Schema Naming Consistency Across Layers

If this rule is not relevant to the code under review, state so and move on.

Schema field names must be consistent between the ADL definition (`.adl`), the Dart schema builder (`_schema.dart`), generated code, and application code that consumes them. Names should clearly indicate their semantics, including units when applicable. Mike flags naming mismatches between layers and field names that omit important context like units.

- Flag field names that omit units when the value has a specific unit (e.g., a timestamp field named `startTime` that holds seconds — should be `startTimestampSeconds`).
- Flag naming mismatches between ADL definitions and their corresponding `_schema.dart` declarations (field names, serialization tokens, and types must agree).
- Flag consumer code that silently converts units without the field name reflecting the stored unit (e.g., reading `startTime` in seconds then multiplying by 1000 for millis — the conversion implies the field name should encode the unit).
- Ensure new schema fields added in one layer are reflected consistently across all layers (ADL, Dart schema, C++/Python producers, Java consumers).

## Example

Bad — field name omits units, consumer silently converts:
```dart
// _schema.dart — 'startTime' gives no indication this is seconds
b.F('startTime', 'st', Dbl(), d: 0.0),
b.F('endTime', 'et', Dbl(), d: 0.0),
```
```dart
// consumer code — silent * 1000 conversion reveals the field is in seconds,
// but the name 'startTime' doesn't say so
int startMillis = (INT(chunkMetadata['startTime']) ?? 0) * 1000;
int endMillis = (INT(chunkMetadata['endTime']) ?? 0) * 1000;
```

Bad — ADL and Dart schema use different names for the same field:
```
// shared.adl
struct ChunkMetadata {
  @SerializedName "ts"
  Double startTimestampSeconds = 0.0;
};
```
```dart
// _schema.dart — field name doesn't match ADL
b.F('startTime', 'ts', Dbl(), d: 0.0),
```

Good — field names include units and match across all layers:
```
// shared.adl
struct ConstituentLazMetadata {
  @SerializedName "ts"
  Double startTimestampSeconds = 0.0;
  @SerializedName "te"
  Double endTimestampSeconds = 0.0;
};
```
```dart
// _schema.dart — matches ADL field names and serialization tokens
b.F('startTimestampSeconds', 'ts', Dbl(), d: 0.0),
b.F('endTimestampSeconds', 'te', Dbl(), d: 0.0),
```
```java
// Java consumer — conversion is obvious because the field name says "Seconds"
lastChunk.startMillis = Math.min(lastChunk.startMillis,
    c.getStartTimestampSeconds() * 1000.0);
```
