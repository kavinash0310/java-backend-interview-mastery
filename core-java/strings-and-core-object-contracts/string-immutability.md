# Why is String immutable in Java?

## Interview-ready answer

A String cannot be changed after it is created. Immutability makes strings safe to share, lets the JVM cache their hash codes, and supports features such as the string pool. It also avoids surprises when strings are used for map keys or passed across application boundaries.

## Simple explanation

An operation that appears to modify a string creates a new String value; it does not edit the original character data. This is useful for common values such as configuration keys, file paths, and protocol text that many parts of an application may read.

## Easy way to remember

A String is a finished label. If the text changes, you get a new label.

## Example

After `name = name + " Lee"`, the variable points to a new String; any other reference to the old value still sees the old text.

## Common mistakes

Immutability does not make every use secure: secrets in strings can remain in memory until collected, so avoid logging credentials. For repeated concatenation in a loop, use StringBuilder.

## Interview follow-ups

What is the string pool? Why are strings convenient HashMap keys? When would you use StringBuilder?

## References

- [Java 21 String API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/String.html)

## Quick revision

Immutable means stable, shareable, and safe as a key; changing text creates another String.
