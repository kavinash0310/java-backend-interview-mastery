# What is the diamond problem, and how does Java handle it?

## Interview-ready answer

The diamond problem is ambiguity when a type inherits the same implementation through two paths. Java avoids multiple inheritance of classes. Interfaces can have default methods, but when unrelated interfaces provide the same default, the implementing class must resolve the conflict.

## Simple explanation

Java’s rules are: a class implementation takes precedence over an interface default; a more-specific interface wins over its parent; otherwise the class must override and can explicitly call InterfaceName.super.method().

## Easy way to remember

If Java cannot tell which default to use, make the choice explicit in the class.

## Example

If Audit and Printable both define default describe(), a class implementing both should override describe() and delegate deliberately.

## Code example

```java
interface A { default String label() { return "A"; } }
interface B { default String label() { return "B"; } }
class C implements A, B {
    public String label() { return A.super.label(); }
}
```

## Common mistakes

The usual OOP diamond question is about class state inheritance; Java does not permit a class to extend two classes. Interface defaults can still produce an implementation conflict.

## Interview follow-ups

Which wins: class method or interface default? How do you call a specific interface default?

## Quick revision

No multiple class inheritance; conflicting interface defaults require an explicit override.
