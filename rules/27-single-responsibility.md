---
summary: "Checks that classes and methods have a single responsibility and don't mix unrelated concerns"
file_patterns: [["*.dart"], ["*.java"]]
tags: ["mikeh", "architecture", "maintainability"]
sources: ["D81681", "D67232", "D54911"]
---
# Single Responsibility: Don't Mix Concerns

If this rule is not relevant to the code under review, state so and move on.

Each class and method should have one clear responsibility. God-objects that mix fetching, parsing, rendering, and persisting are hard to test and maintain. Mike flags classes that take on too many responsibilities and recommends decomposing into focused collaborators.

- Flag classes that perform multiple unrelated operations (e.g., a service that fetches data, transforms it, renders UI, and persists results).
- Flag methods longer than ~50 lines that handle multiple distinct steps — suggest extracting private helpers.
- Each class should be describable in one sentence without "and."

## Example

Bad:
```dart
class ModuleManager {
  void syncModule(String id) {
    // Fetches from server (network concern)
    final raw = http.get('/modules/$id');
    // Parses response (serialization concern)
    final module = Module.fromJson(raw);
    // Updates local cache (persistence concern)
    _cache.put(id, module);
    // Updates UI (presentation concern)
    _notifier.notify(module);
  }
}
```

Good:
```dart
class ModuleSyncService {
  final ModuleClient _client;
  final ModuleCache _cache;
  final ModuleNotifier _notifier;

  void syncModule(String id) {
    final module = _client.fetch(id);
    _cache.put(id, module);
    _notifier.notify(module);
  }
}
```
