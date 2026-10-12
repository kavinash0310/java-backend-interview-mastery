# Method overloading vs overriding

## Interview-ready answer

Overloading uses the same method name with different parameter lists, and the compiler selects a match from the declared types. Overriding replaces an inherited instance method, and runtime dispatch selects the implementation for the actual object.

## Simple explanation

Return type alone cannot distinguish overloads. An override must keep a compatible signature and may narrow the returned type or make access wider, but not weaker. Static methods are hidden rather than overridden; private and final methods cannot be overridden.

## Easy way to remember

Overloading is chosen while compiling the call; overriding is chosen while the program runs.

## Example

`print(int)` and `print(String)` are overloads. A PremiumAccount subclass overriding `calculateFee()` replaces the inherited behavior at runtime.

## Common mistakes

Do not assume overload selection follows the runtime class of an argument. Avoid overload sets that become ambiguous with null or autoboxing.

## Interview follow-ups

What is method hiding? Can an overriding method throw a broader checked exception?

## Quick revision

Different parameters: overload. Same inherited signature, new subtype behavior: override.
