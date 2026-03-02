---
summary: "Checks that streams, subscriptions, controllers, and connections are properly disposed in all code paths"
file_patterns: [["*.dart"], ["*.java"]]
tags: ["mikeh", "correctness", "resources"]
sources: ["D67232", "D81681", "D86985"]
---
# Dispose Resources in All Code Paths

If this rule is not relevant to the code under review, state so and move on.

Code that opens streams, creates subscriptions, acquires connections, or allocates controllers must dispose of them in all code paths — including error paths. Leaked resources cause memory growth, stale listeners, and subtle bugs. Mike emphasizes proper cleanup especially for async resources and during invalidation.

- Flag classes that create `StreamSubscription`, `StreamController`, `WebSocket`, or similar resources without a corresponding `close`/`cancel`/`dispose`.
- Flag error paths (`catch` blocks) that skip resource cleanup.
- Flag cleanup code (closing files, cancelling futures) that is not in a `finally` block or `dispose()` method.
- Ensure `dispose()` or `close()` is called in `finally` blocks or equivalent lifecycle methods.

## Example

Bad:
```dart
class Watcher {
  late StreamSubscription _sub;
  void start(Stream<Event> events) {
    _sub = events.listen(_onEvent);
  }
  // No dispose — subscription leaks
}
```

Good:
```dart
class Watcher {
  StreamSubscription? _sub;
  void start(Stream<Event> events) {
    _sub = events.listen(_onEvent);
  }
  void dispose() {
    _sub?.cancel();
    _sub = null;
  }
}
```
