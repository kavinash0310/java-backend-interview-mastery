# What is a functional interface?

## Interview-ready answer

A functional interface has one abstract method, so a lambda can provide its implementation. It may also have default and static methods. @FunctionalInterface asks the compiler to check that the interface stays compatible with that rule.

## Simple explanation

Runnable, Predicate, Function, and Consumer are common examples. The compiler can ignore abstract methods that merely match public methods of Object when determining the functional method.

## Easy way to remember

One abstract job, many possible lambda implementations.

## Example

A Predicate<Order> can answer whether an order is eligible for shipment.

## Code example

```java
@FunctionalInterface
interface DiscountRule {
    int apply(int subtotalCents);
}
DiscountRule tenPercent = subtotal -> subtotal * 10 / 100;
```

## Common mistakes

A functional interface is not the same thing as a lambda; it is the target type that gives the lambda a method signature. Do not mark an interface functional if it has two unrelated abstract methods.

## Interview follow-ups

Can it have default methods? Name a standard functional interface from java.util.function.

## Quick revision

One abstract method enables a lambda; the annotation protects the contract.
