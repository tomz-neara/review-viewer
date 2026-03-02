---
summary: "Checks that async operations track their lifecycle state and handle stale results after invalidation"
file_patterns: [["*.dart"], ["*.java"]]
tags: ["mikeh", "correctness", "async"]
sources: ["D67232", "D81681"]
---
# Track Async Operation Lifecycle

If this rule is not relevant to the code under review, state so and move on.

Async operations must track whether they are pending, completed, stale, or cancelled. When a dependency changes while an async operation is in flight, the result may be stale — callers must be able to distinguish a valid result from a stale one. Mike scrutinizes whether pending state is captured and stale state is handled.

- Flag async operations that don't track their in-flight state.
- Flag code that uses an async result without checking if the inputs changed since the request was made.
- Ensure callers can distinguish between running, valid, stale, and error states.

## Example

Bad:
```dart
Future<Data> fetchData() async {
  // No tracking of whether this is stale
  return await _api.get(currentQuery);
}
```

Good:
```dart
Future<Data> fetchData() async {
  final queryAtStart = currentQuery;
  _state = FetchState.pending;
  final result = await _api.get(queryAtStart);
  if (queryAtStart != currentQuery) {
    _state = FetchState.stale;
    return result;  // caller knows it's stale
  }
  _state = FetchState.valid;
  return result;
}
```
