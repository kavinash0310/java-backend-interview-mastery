# How do you create an immutable class?

## Interview-ready answer

Make the state private and final, set it completely in the constructor, provide no mutators, and prevent subclasses from changing the rules. Defensively copy mutable inputs and avoid returning internal mutable objects.

## Simple explanation

A final reference does not make the referenced object immutable. If a class stores a Date or a mutable list, copy it on input and either return a defensive copy or expose a genuinely unmodifiable view over an unshared copy.

## Easy way to remember

Build it once, keep the inside private, and never hand out the mutable parts.

## Example

A Money value object can store a currency and amount; operations such as add return a new Money instead of modifying the current one.

## Code example

```java
public final class Team {
    private final List<String> members;
    public Team(List<String> members) {
        this.members = List.copyOf(members);
    }
    public List<String> members() { return members; }
}
```

## Common mistakes

Collections.unmodifiableList does not protect against changes through another reference to the original list; copy first. Be careful with arrays and mutable nested objects.

## Interview follow-ups

Why are immutable objects useful in concurrent code? Is a final field enough?

## Quick revision

Private final state, no setters, defensive copies, no mutable escape.
