# What is the difference between == and equals() in Java?

## Interview-ready answer

For primitives, == compares values. For object references, == asks whether both references point to the same object; equals() asks whether the objects should count as equal by their class’s definition. String overrides equals() to compare text.

## Simple explanation

Two String objects can contain the same characters but be different objects. String literals may be pooled, which can make == appear to work in a small example. That is an implementation feature of literal reuse, not a safe way to compare text.

## Easy way to remember

== asks “same box?”; equals() asks “same contents according to this class?”

## Example

`new String("cat") == new String("cat")` is false, while their equals() result is true.

## Code example

```java
String a = new String("cat");
String b = new String("cat");
System.out.println(a == b);      // false
System.out.println(a.equals(b)); // true
```

## Common mistakes

Use == for enum values and deliberate identity checks. Use equals() for logical object comparison. Guard against null when calling equals(), or use Objects.equals(a, b).

## Interview follow-ups

What happens if a class inherits Object.equals()? Why can String literals make == confusing?

## References

- [Java 21 String API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/String.html)
- [Java 21 Object API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html)

## Quick revision

Primitive == compares values; reference == compares identity; equals() compares logical equality.
