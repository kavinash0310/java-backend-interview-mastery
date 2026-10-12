# Explain the four pillars of OOP with project examples

## Interview-ready answer

Encapsulation protects an object’s state, abstraction exposes the useful operation, inheritance shares or specializes behavior, and polymorphism lets callers use one contract with different implementations.

## Simple explanation

In an order service, an Order can keep its status private and expose methods that enforce valid transitions (encapsulation). A PaymentProcessor interface hides provider details (abstraction). Card and wallet processors can implement that contract (polymorphism). Inheritance is useful for a genuine shared “is-a” relationship, but composition is often safer for assembling behavior.

## Easy way to remember

Protect state, hide detail, reuse carefully, and call through a common contract.

## Example

`checkout()` calls PaymentProcessor. The actual card or wallet implementation can change without changing checkout orchestration.

## Common mistakes

Do not describe inheritance as the only reuse tool. Avoid public setters that let callers create impossible states. Keep the example tied to a real responsibility.

## Interview follow-ups

When is composition better than inheritance? How does polymorphism help testability?

## Quick revision

Encapsulation protects; abstraction hides detail; inheritance specializes; polymorphism swaps implementations.
