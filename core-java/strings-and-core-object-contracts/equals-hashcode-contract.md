# Why should equals() and hashCode() be overridden together?

## Interview-ready answer

If two objects are equal according to equals(), they must return the same hash code. Hash-based collections use hashCode() to find a bucket and equals() to identify the matching key. Overriding only equals() can make equal objects look like different keys in a HashMap or HashSet.

## Simple explanation

The reverse is not required: two unequal objects may have the same hash. That is a collision, and the collection resolves it with equals(). The rule matters most when your own class is used as a map key or set member.

## Easy way to remember

Equal objects must point to the same shelf; different objects are allowed to share a shelf.

## Example

Two separately-created EmployeeKey objects for employee 42 should be interchangeable in a HashMap if employeeId defines equality.

## Code example

```java
record EmployeeKey(long employeeId) {}
Map<EmployeeKey, String> names = new HashMap<>();
names.put(new EmployeeKey(42), "Asha");
String name = names.get(new EmployeeKey(42)); // Asha
```

## Common mistakes

Do not include mutable fields in equality if they can change while the object is a key. Do not compare only by hash code; collisions are legal. Records generate these methods from their components.

## Interview follow-ups

Can unequal objects share a hash code? What breaks if a key changes after insertion?

## References

- [Java 21 Object API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html)

## Quick revision

equals true implies same hash; same hash does not imply equals.
