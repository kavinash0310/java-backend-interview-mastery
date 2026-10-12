# How does HashMap work internally?

## Interview-ready answer

A HashMap turns a key’s hash into a bucket index. It stores entries in that bucket, then uses equals() to find the exact key. Collisions share a bucket; in modern Java, a crowded bucket can become a balanced tree. Resizing spreads entries again when the map grows.

## Simple explanation

put(key, value) spreads the key’s hash, picks a bucket, and checks entries there. If an equal key already exists, its value is replaced; otherwise a new entry is added. get(key) follows the same route and checks equality after the hash match. Capacity grows after the load threshold is crossed, which costs work but keeps normal lookups fast.

## Easy way to remember

Think of the hash as the shelf number and equals() as reading the label on the box.

## Example

If two customer IDs land on the same shelf, the map checks both labels. A collision does not make the values interchangeable.

## Code example

```java
Map<String, Integer> scores = new HashMap<>();
scores.put("Mina", 90);
scores.put("Mina", 95); // replaces the value for the equal key
int score = scores.get("Mina"); // 95
```

## Common mistakes

HashMap does not guarantee iteration order and is not thread-safe. A key’s hash and equality must stay stable while it is in the map. Java 8 introduced tree bins for heavily crowded buckets in common OpenJDK implementations; exact thresholds are implementation details, not a contract to code against.

## Interview follow-ups

What happens during resize? Why must keys be immutable or stable? What is the average and worst-case lookup cost?

## References

- [Java 21 HashMap API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/HashMap.html)

## Quick revision

Hash chooses a bucket; equals finds the key; collisions are handled inside the bucket; resizing controls load.
