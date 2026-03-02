---
summary: "Checks that shared mutable state uses thread-safe collections and proper synchronization"
file_patterns: [["*.java"]]
tags: ["petro", "java", "concurrency", "correctness"]
sources: ["D56404", "D88515", "D54911", "D50960", "D70717"]
---
# Thread Safety for Shared State

If this rule is not relevant to the code under review, state so and move on.

Concurrency bugs are among the hardest to reproduce and diagnose. Petro scrutinizes code that touches shared mutable state from multiple threads, flagging missing synchronization, non-atomic compound operations, and use of non-thread-safe collections in concurrent contexts.

- Flag `HashMap`, `HashSet`, `ArrayList` used as fields written by multiple threads — use `ConcurrentHashMap`, `ConcurrentHashMap.newKeySet()`, or `CopyOnWriteArrayList`.
- Flag read-modify-write sequences on shared state without synchronization (e.g., `if (!map.containsKey(k)) map.put(k, v)`) — use `computeIfAbsent` or explicit locking.
- Flag shared mutable fields that lack `volatile`, `AtomicReference`, or synchronization when accessed across threads.
- Flag side-effectful work (email sending, HTTP calls, job scheduling) performed inside a database transaction — move it outside the transaction boundary to prevent blocking and inconsistency.
- Flag `SELECT FOR UPDATE` without a clear need — unnecessary locking causes contention and potential deadlocks.

## Example

Bad:
```java
class JobScheduler {
    private final Map<String, Job> activeJobs = new HashMap<>();  // not thread-safe

    public void submit(Job job) {
        if (!activeJobs.containsKey(job.getId())) {  // check-then-act race
            activeJobs.put(job.getId(), job);
            executor.submit(job);
        }
    }
}
```

Good:
```java
class JobScheduler {
    private final ConcurrentHashMap<String, Job> activeJobs = new ConcurrentHashMap<>();

    public void submit(Job job) {
        if (activeJobs.putIfAbsent(job.getId(), job) == null) {  // atomic
            executor.submit(job);
        }
    }
}
```
