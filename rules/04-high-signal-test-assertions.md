---
summary: "Checks that test assertions are specific enough that broken logic cannot accidentally pass"
file_patterns: [["*_test.dart"], ["*_test.py"], ["*Test.java"]]
tags: ["mikeh", "testing"]
sources: ["D70026", "D56510", "D67232"]
---
# Write High-Signal Test Assertions

If this rule is not relevant to the code under review, state so and move on.

Test assertions must be specific enough that a broken implementation cannot accidentally satisfy them. Assert exact values, not just counts or non-null checks. Apply this litmus test: *"Can I make this test pass by breaking the logic in a way that makes it incorrect?"* If yes, the assertion is insufficient.

Mike's principle: *"lol, don't just expect num features, expect the features that you expect to survive. The general rule I like to apply is, 'can I make this test pass by breaking the logic in a way that makes it incorrect, but the test passes because the expect is insufficient'."*

- Flag assertions that only check `.length`, `.size()`, `isNotNull`, or `isNotEmpty` when specific values could be asserted.
- Assert the actual content, not just the shape of the result.
- For collections, assert the expected elements, not just the count.

## Example

Bad:
```dart
expect(deduped.length, 3);
expect(result, isNotNull);
```

Good:
```dart
expect(deduped, containsAll([featureA, featureB, featureC]));
expect(result.name, equals('expectedName'));
```
