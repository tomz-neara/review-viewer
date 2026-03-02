---
summary: "Checks that tests are isolated, deterministic, and free of shared mutable state or real network calls"
file_patterns: [["*.java"]]
tags: ["petro", "java", "testing", "reliability"]
sources: ["D58497", "D51959", "D65734", "D64024", "D51517"]
---
# Test Isolation and Determinism

If this rule is not relevant to the code under review, state so and move on.

Flaky tests erode trust in the suite and waste everyone's time. Petro consistently flags tests that share mutable state, make real network calls, rely on timing, or depend on non-deterministic iteration order. Every test should produce the same result regardless of execution order, parallelism, or environment.

- Flag tests that share mutable static fields or class-level state between test methods — use fresh instances or `@BeforeEach` setup.
- Flag tests that make real external calls (HTTP, AWS, database servers) — mock or use local fakes.
- Flag `Thread.sleep` or fixed delays used to wait for async results — assert on state or use `CountDownLatch` / `Awaitility`.
- Flag assertions on collection order when the source is unordered (`Set`, `HashMap`) — sort before comparing or use order-independent assertions like `containsExactlyInAnyOrder`.
- Flag floating-point equality checks without epsilon tolerance — use `isCloseTo` or equivalent.
- Flag duplicated test setup that should be extracted into shared helpers or fixtures.

## Example

Bad:
```java
class SyncServiceTest {
    static List<String> results = new ArrayList<>();  // shared mutable state

    @Test void testSync() {
        service.sync(module);
        Thread.sleep(500);  // timing-dependent
        assertEquals(Set.of("a", "b", "c").toString(), results.toString());  // order-dependent
    }
}
```

Good:
```java
class SyncServiceTest {
    private List<String> results;  // fresh per test

    @BeforeEach void setUp() { results = new ArrayList<>(); }

    @Test void testSync() {
        service.sync(module);
        await().atMost(Duration.ofSeconds(2)).until(() -> results.size() == 3);
        assertThat(results).containsExactlyInAnyOrder("a", "b", "c");
    }
}
```
