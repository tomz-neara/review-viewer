---
summary: "Checks that verbose or roundabout code is simplified when a direct alternative exists"
file_patterns: [["*"]]
tags: ["mikeh", "readability"]
sources: ["D56510", "D54911", "D86985"]
---
# Simplify Verbose Logic

If this rule is not relevant to the code under review, state so and move on.

When code does something in a verbose or roundabout way and a simpler, more direct alternative exists, prefer the simpler version. This includes: using built-in methods instead of manual loops, using ternary expressions for simple conditionals, using `.map()` instead of explicit iteration, and using language idioms.

Mike's principles: *"can you just do `min_timestamp_chunk = 0 if timestamps.size == 0 else min(timestamps)`?"*, *"you can check `.getDisc` - no need to use `fromString(...)`"*, *"four lines above is just map over `m()`"*

- Flag manual loops that could be replaced with `.map()`, `.where()`, `.any()`, `.expand()`.
- Flag verbose if/else blocks that could be a ternary or null-coalescing expression.
- Flag unnecessary type conversions or round-trips (e.g. `fromString(x.toString())`).
- The simpler version must be equally readable — don't over-compress.

## Example

Bad:
```dart
List<String> names = [];
for (final item in list) {
  names.add(item.name);
}

String value;
if (input == null) {
  value = 'default';
} else {
  value = input;
}
```

Good:
```dart
final names = list.map((Item item) => item.name).toList();

final value = input ?? 'default';
```
