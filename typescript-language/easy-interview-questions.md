# TypeScript: Easy Interview Questions and Answers

**40 questions.** These answers are short enough to rehearse and specific enough to expand with an example from a real application.

## Types and everyday syntax

### 1. What is TypeScript?

**Answer:** TypeScript is JavaScript with a static type system that checks many mistakes before code runs and then emits JavaScript.

### 2. Does TypeScript run directly in a browser?

**Answer:** Usually not as TypeScript source; a compiler or build tool transforms it into JavaScript first.

### 3. What is a type annotation?

**Answer:** It explicitly describes the expected type, such as let age: number = 30.

### 4. What is type inference?

**Answer:** The compiler derives a type from context or an initializer, so you often do not need to write annotations.

### 5. What is the type string?

**Answer:** It represents text values; number represents numeric values and boolean represents true or false.

### 6. What is an array type?

**Answer:** It describes an ordered collection, for example number[] or Array<number>.

### 7. What is a tuple?

**Answer:** A tuple is an array type with a fixed known position structure, such as [string, number].

### 8. What is an interface?

**Answer:** An interface describes the shape of an object and can be implemented or extended by compatible types.

### 9. What is a type alias?

**Answer:** A type alias gives a name to any type expression, including unions, primitives, and object shapes.

### 10. What is a union type?

**Answer:** It allows a value to be one of several types, such as string | number.

## Narrowing and generics

### 11. What is an intersection type?

**Answer:** It combines requirements from multiple types, often with A & B for an object that has both shapes.

### 12. What does any mean?

**Answer:** any disables many type checks for a value; using it broadly gives up much of TypeScript’s safety.

### 13. What does unknown mean?

**Answer:** unknown accepts any input but requires checking or narrowing before the value is used as a specific type.

### 14. What is never?

**Answer:** never represents a value that cannot occur, such as a function that always throws or an impossible narrowed case.

### 15. What does void mean for a function?

**Answer:** It indicates callers should not rely on a meaningful returned value.

### 16. What is an optional property?

**Answer:** A property marked with ? may be absent, for example nickname?: string.

### 17. What does readonly do?

**Answer:** It prevents assignments through that typed property reference; it does not deeply freeze the object at runtime.

### 18. What is an enum?

**Answer:** An enum defines a named set of values, though string unions or const objects are often simpler for application code.

### 19. What is a generic?

**Answer:** A generic parameter lets a function or type preserve relationships between input and output types.

### 20. What is a type assertion?

**Answer:** It tells the compiler to treat an expression as a type; it performs no runtime validation.

## Advanced type design

### 21. What is a function parameter type?

**Answer:** It declares which values a function accepts, helping callers and implementation code stay consistent.

### 22. What is a return type annotation?

**Answer:** It states what a function returns, enabling checks on every return path.

### 23. What is a nullable value?

**Answer:** It may be null or undefined; strictNullChecks makes those possibilities visible in the type system.

### 24. What does strict mode do in TypeScript?

**Answer:** The strict compiler option enables a group of stronger checks, including strict null and function checks.

### 25. What is a module in TypeScript?

**Answer:** A file with top-level import or export is treated as a module with its own scope.

### 26. How do you export a type?

**Answer:** Use export type or export an interface/type alias so another module can import it.

### 27. What is a class in TypeScript?

**Answer:** It describes and creates objects with fields and methods, with optional access modifiers and type annotations.

### 28. What does public mean on a class member?

**Answer:** It makes the member accessible from any code; public is the default visibility.

### 29. What does private mean in a class?

**Answer:** TypeScript’s private modifier restricts access during type checking, but is not the same as JavaScript’s runtime #private field.

### 30. What is a constructor parameter property?

**Answer:** A constructor parameter with a modifier such as readonly can declare and initialize a class field in one place.

## Type system edge cases

### 31. What is a type guard?

**Answer:** A check or predicate that narrows a broader type to a more specific type within a code path.

### 32. What does typeof narrow?

**Answer:** It narrows primitive unions using runtime checks such as typeof value === string.

### 33. What does instanceof check?

**Answer:** It checks whether an object’s prototype chain includes a constructor’s prototype, often narrowing class instances.

### 34. What is a literal type?

**Answer:** It represents a specific value, such as status: active, rather than every string.

### 35. What is a discriminated union?

**Answer:** It is a union whose members share a literal-valued property that lets code narrow safely by checking that property.

### 36. What is the purpose of tsconfig.json?

**Answer:** It configures the TypeScript compiler, including target, module format, strictness, included files, and output.

### 37. What does target mean in compiler options?

**Answer:** It selects the JavaScript language level emitted by the compiler; it does not polyfill missing runtime APIs.

### 38. What is a declaration file?

**Answer:** A .d.ts file describes types for JavaScript code without providing its implementation.

### 39. What is a utility type?

**Answer:** A built-in type transformation such as Partial<T> or Pick<T, K> that derives a type from another.

### 40. What is an optional parameter?

**Answer:** A parameter marked with ? may be omitted by the caller and is typed as potentially undefined.

## References

- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- [Everyday Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)
- [Narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)
- [Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
- [Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html)
