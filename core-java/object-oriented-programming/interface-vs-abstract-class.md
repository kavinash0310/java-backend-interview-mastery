# Interface vs abstract class — when would you choose each?

## Interview-ready answer

Use an interface to describe a capability or contract that unrelated classes can implement. Use an abstract class when related classes need shared state or a common base implementation. A class can implement several interfaces but extend only one class.

## Simple explanation

Interfaces can include abstract methods, constants, and—since Java 8—default and static methods; private helper methods were added later. Abstract classes can have constructors, instance fields, and methods with different access levels. Pick the smallest contract that supports the design.

## Easy way to remember

An interface says what a type can do; an abstract class can also provide the shared starting point.

## Example

`Refundable` can be implemented by cards and wallets. `BasePayment` can share transaction ID and audit behavior across payment types.

## Common mistakes

Do not add an abstract superclass just to avoid a few duplicated lines. Do not use default methods to hide a design with unclear ownership.

## Interview follow-ups

Can an interface have method implementations? Can Java inherit from multiple classes?

## References

- [Java 21 interfaces tutorial](https://docs.oracle.com/javase/tutorial/java/IandI/createinterface.html)

## Quick revision

Interface for capabilities and multiple contracts; abstract class for shared state and implementation.
