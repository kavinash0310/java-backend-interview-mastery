# TypeScript: Medium Interview Questions and Answers

**40 questions.** These answers are short enough to rehearse and specific enough to expand with an example from a real application.

## Types and everyday syntax

### 1. How does structural typing work in TypeScript?

**Answer:** Compatibility is usually based on whether a value has the required members, not whether it declares a particular class or interface.

### 2. How are excess property checks different from structural assignment?

**Answer:** Object literals receive an extra check for likely misspelled properties; assigning an existing variable may follow ordinary structural compatibility.

### 3. When should you choose an interface over a type alias?

**Answer:** Both describe object shapes; interfaces support declaration merging and extension, while aliases express unions and other type operators directly.

### 4. How do optional properties differ from properties whose value can be undefined?

**Answer:** An optional property may be absent; a required property typed T | undefined must exist but may hold undefined.

### 5. What does strictNullChecks prevent?

**Answer:** It makes null and undefined distinct types that must be handled instead of silently assignable to other types.

### 6. What is control-flow narrowing?

**Answer:** The compiler refines a variable’s type along a branch based on checks, assignments, and code flow.

### 7. How would you narrow a union of API result shapes?

**Answer:** Use a shared literal discriminator and branch on it; each branch then has the matching result fields.

### 8. What is a user-defined type predicate?

**Answer:** A function returning value is Type lets a successful boolean check narrow that value for callers.

### 9. How do you check an unknown value before using it?

**Answer:** Validate its runtime shape with typeof, Array.isArray, property checks, or a schema library, then narrow it.

### 10. Why is a type assertion not data validation?

**Answer:** It changes the compiler’s belief only; malformed network or storage data remains malformed at runtime.

## Narrowing and generics

### 11. What is keyof useful for?

**Answer:** It creates a union of the known property keys of a type and supports safe generic property operations.

### 12. What does indexed access type T[K] do?

**Answer:** It selects the type of property K on T, useful for deriving parameter or field types.

### 13. What is a mapped type?

**Answer:** It iterates over keys of a type to create a transformed object type, as built-in Partial does.

### 14. How does Partial<T> work?

**Answer:** It makes each property optional at the type level; it does not change an object at runtime.

### 15. How do Pick<T, K> and Omit<T, K> differ?

**Answer:** Pick keeps selected keys; Omit removes selected keys from the resulting type.

### 16. What does Record<K, V> describe?

**Answer:** An object type with keys from K and values of type V, useful for lookup tables.

### 17. What does ReturnType<typeof fn> derive?

**Answer:** It extracts the declared return type of a function type.

### 18. What is a generic constraint?

**Answer:** It limits allowed generic arguments to types that satisfy a required shape, for example T extends { id: string }.

### 19. Why can an unconstrained generic not access an arbitrary property?

**Answer:** The compiler must support every possible T; constrain T or pass the needed operation as a callback.

### 20. What is a generic default?

**Answer:** It supplies a type argument when a caller does not specify one, while allowing callers to override it.

## Advanced type design

### 21. How can a function preserve a relationship between input and output types?

**Answer:** Use the same generic parameter in both positions, such as identity<T>(value: T): T.

### 22. What is overload signature in TypeScript?

**Answer:** It gives callers multiple valid call shapes while one implementation handles the runtime behavior.

### 23. What is the difference between overloads and union parameters?

**Answer:** Overloads express distinct input-output relationships; a union parameter is simpler when one shared result type is sufficient.

### 24. What is a function type?

**Answer:** It describes parameter and return types, for example (id: string) => User.

### 25. What are optional and default parameters?

**Answer:** Optional parameters may be omitted; default parameters supply a runtime value when the argument is undefined.

### 26. What does the satisfies operator do?

**Answer:** It checks that an expression meets a target type while preserving the expression’s more specific inferred type.

### 27. How does as const affect an expression?

**Answer:** It keeps literal values narrow and marks object properties or arrays readonly at the type level.

### 28. What is a const assertion not?

**Answer:** It is not runtime freezing; JavaScript objects remain mutable unless separately frozen.

### 29. How do readonly arrays differ from mutable arrays?

**Answer:** ReadonlyArray<T> prevents mutation through that reference, but another mutable alias can still change the underlying array.

### 30. What is a type-only import?

**Answer:** import type is erased from emitted JavaScript and communicates that an import is needed only for type checking.

## Type system edge cases

### 31. What is declaration merging?

**Answer:** Certain declarations with the same name, such as interfaces, can combine their members during type checking.

### 32. Why can namespace and module declarations behave differently?

**Answer:** Modules use import/export file scope; namespaces are a separate construct and generally are not the preferred organization for modern apps.

### 33. What does moduleResolution control?

**Answer:** It describes how TypeScript finds modules and package entry points, aligned with the runtime or bundler environment.

### 34. What does noEmit do?

**Answer:** It asks TypeScript to type-check without writing JavaScript, often because another tool handles the build.

### 35. Why can TypeScript compile when runtime code still fails?

**Answer:** Types are erased and do not guarantee environment APIs, valid external data, correct logic, or successful network operations.

### 36. How should you type an API response?

**Answer:** Use a type for the expected shape, but validate untrusted JSON at runtime before treating it as that type.

### 37. What does a non-null assertion do?

**Answer:** The postfix ! tells the compiler a value is not null or undefined without adding a runtime check.

### 38. Why should non-null assertions be limited?

**Answer:** They silence a useful check and can turn a missing value into a runtime error.

### 39. What is an ambient declaration?

**Answer:** It declares the types of a global or external JavaScript API whose implementation exists elsewhere.

### 40. How does TypeScript infer a callback parameter?

**Answer:** It often uses the expected function type from the API context, allowing the parameter type to be inferred.

## References

- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- [Everyday Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)
- [Narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)
- [Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
- [Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html)
