---
summary: "Checks that variables never reassigned are declared final and that input parameters are not mutated"
file_patterns: [["*.java"]]
tags: ["petro", "java", "immutability"]
sources: ["D76216", "D50996", "D56979"]
---
# Prefer Final Variables

If this rule is not relevant to the code under review, state so and move on.

Variables that are assigned once and never reassigned should be declared `final`. Mutating input parameters makes callers' state unpredictable and hides side effects. Petro consistently flags missing `final` modifiers — immutability by default communicates intent and prevents accidental reassignment.

- Flag local variables that are assigned once and never reassigned but lack `final`.
- Flag method parameters that are reassigned inside the method body — introduce a local variable instead.
- Flag fields initialized in the constructor and never written again that are not `final`.
- Collections returned from methods should be wrapped in unmodifiable views when the caller should not mutate them.

## Example

Bad:
```java
public void processOrder(Order order) {
    String status = order.getStatus();       // never reassigned, should be final
    List<Item> items = order.getItems();
    order.setStatus("PROCESSING");           // mutating the input parameter
    for (Item item : items) {
        BigDecimal price = item.getPrice();  // never reassigned
        applyDiscount(price);
    }
}
```

Good:
```java
public void processOrder(Order order) {
    final String status = order.getStatus();
    final List<Item> items = order.getItems();
    final Order updated = order.withStatus("PROCESSING");  // return new instance
    for (final Item item : items) {
        final BigDecimal price = item.getPrice();
        applyDiscount(price);
    }
}
```
