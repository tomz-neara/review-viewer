---
summary: "Checks that catch blocks log errors with context and that log calls use parameterized formatting"
file_patterns: [["*.dart"], ["*.java"], ["*.py"]]
tags: ["petro", "observability", "error-handling"]
sources: ["D50944", "D57121", "D51447"]
---
# Structured Logging

If this rule is not relevant to the code under review, state so and move on.

Effective logging is the difference between a five-minute diagnosis and hours of guesswork. Petro consistently flags catch blocks that swallow errors silently, log messages that lack context, and string concatenation where parameterized formatting should be used. Every error log should answer: what operation failed, on which entity, and why.

- Flag catch blocks that neither log the exception nor rethrow it — errors must not be silently swallowed.
- Flag log calls missing identifying context (entity IDs, operation name, relevant parameters).
- Flag string concatenation in log messages (e.g. `"Failed " + id`) — use parameterized formatting (`log.warning('Failed to sync module {}', moduleId)`) for performance and consistency.
- Flag error handling that logs only the exception message but discards the stack trace.
- This complements rule 20 (remove debug prints) — production code should use proper logging, not print statements.

## Example

Bad:
```java
try {
    syncModule(module);
} catch (IOException e) {
    log.error("sync failed");  // no context: which module? what error?
}
```

Good:
```java
try {
    syncModule(module);
} catch (IOException e) {
    log.error("Failed to sync module {}: {}", module.getId(), e.getMessage(), e);
}
```
