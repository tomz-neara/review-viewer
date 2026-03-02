---
summary: "Checks that serialization, projection, or transformation code includes round-trip tests"
file_patterns: [["*"]]
tags: ["mikeh", "testing", "correctness"]
sources: ["D81681", "D70026", "D56510"]
---
# Require Round-Trip Tests for Reversible Transformations

If this rule is not relevant to the code under review, state so and move on.

Any code that serializes/deserializes, encodes/decodes, or transforms/inverts data must include a round-trip test proving `f_inverse(f(x)) == x`. This applies to coordinate projections, JSON/protobuf encoding, ADL type conversion, and database read/write paths. Test with representative inputs including edge cases.

Mike's principle: *"maybe write a small test to verify that forwards -> backwards is a clean round trip for the two kinds of inputs?"*

- Flag serialization or transformation code that lacks a corresponding round-trip test.
- Round-trip tests catch subtle data loss, precision errors, and encoding bugs.
- Include edge cases: empty input, maximum values, special characters.

## Example

Bad:
```dart
// Only tests one direction
test('encode produces expected bytes', () {
  expect(encode(input), expectedBytes);
});
```

Good:
```dart
// Tests the full round trip
test('encode/decode round trip preserves data', () {
  final inputs = [simpleCase, edgeCase, emptyCase];
  for (final input in inputs) {
    expect(decode(encode(input)), equals(input));
  }
});
```
