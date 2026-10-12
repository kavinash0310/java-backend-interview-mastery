# Explain Java 8 features, Streams, and lambda expressions

## Interview-ready answer

Lambdas let us pass behavior as a value, and Streams let us describe a pipeline of operations over data. Java 8 also brought functional interfaces, Optional, and the java.time API. A stream is usually lazy until a terminal operation runs.

## Simple explanation

A pipeline might filter orders, map them to totals, and sum the results. Intermediate steps describe work; a terminal step such as collect or sum performs it. Streams are not collections and are normally consumed once.

## Easy way to remember

A lambda supplies the “what to do”; a stream pipeline describes “which data and in what steps.”

## Example

Filter paid orders, map them to amounts, then total the amounts. For simple loops with side effects, an ordinary loop can still be clearer.

## Code example

```java
long total = orders.stream()
    .filter(Order::isPaid)
    .mapToLong(Order::amountInCents)
    .sum();
```

## Common mistakes

Avoid side effects in stream operations, do not reuse a stream, and do not assume parallelStream is faster. Use Optional for a meaningful possibly-absent result, not as a field type by default.

## Interview follow-ups

What is lazy evaluation? What is the difference between map() and flatMap()?

## References

- [Java 21 Stream API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/stream/Stream.html)
- [Java 21 Date-Time API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/package-summary.html)

## Quick revision

Lambdas pass behavior; streams compose data steps; terminal operations execute the pipeline.
