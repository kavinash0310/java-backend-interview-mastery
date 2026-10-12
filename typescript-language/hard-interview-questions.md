# TypeScript: Hard Interview Questions and Answers

**40 questions.** These answers are short enough to rehearse and specific enough to expand with an example from a real application.

## Types and everyday syntax

### 1. How would you model a discriminated union for loading, success, and error states?

**Answer:** Use a status literal on each member, with data only on success and an error only on failure; branch on status to avoid impossible combinations.

### 2. How would you model a type-safe event map?

**Answer:** Map event names to payload types, then make emit and subscribe generic over a key so each name keeps its payload relationship.

### 3. How can conditional types select a result type?

**Answer:** Use T extends U ? X : Y; distribute over unions only when the checked type parameter is naked on the left of extends.

### 4. What does distributive conditional typing mean?

**Answer:** A conditional over a naked generic parameter is applied separately to each union member; wrapping it in a tuple disables distribution.

### 5. How do infer and conditional types work together?

**Answer:** infer captures part of a type inside a conditional pattern, such as extracting a Promise’s resolved value.

### 6. What is the difference between unknown and never in generic API design?

**Answer:** unknown safely represents an input not yet understood; never represents an impossible value or an operation with no normal result.

### 7. How do variance and function parameter types affect assignability?

**Answer:** Function parameter compatibility depends on strictFunctionTypes and structural relationships; avoid assuming class-style invariance.

### 8. Why can method parameters behave differently from function properties?

**Answer:** TypeScript retains some bivariance behavior for method signatures for compatibility, while function properties receive stricter variance checks.

### 9. How would you type a function that preserves tuple arguments?

**Answer:** Use a generic tuple parameter, such as function call<T extends unknown[]>(...args: T), to retain each argument position.

### 10. How do you preserve literal values in a configuration object?

**Answer:** Use satisfies to check its shape while retaining inferred literals, or use a const assertion when readonly literal inference is desired.

## Narrowing and generics

### 11. What is a branded type?

**Answer:** It intersects a primitive or object with a unique marker to distinguish values that share the same runtime representation.

### 12. Why does a branded type need a validation boundary?

**Answer:** The brand is erased at runtime; only validated construction should create the branded value.

### 13. How can you model mutually exclusive options?

**Answer:** Use a discriminated union or optional-never fields so invalid combinations are rejected by the checker.

### 14. What is an exhaustive switch check?

**Answer:** Assign the remaining union value to never in a default branch; the compiler flags new cases that were not handled.

### 15. How can template literal types constrain string patterns?

**Answer:** They combine literal types with substitutions, such as a prefix and a union of permitted route names.

### 16. What are key remapping in mapped types?

**Answer:** A mapped type can use an as clause to rename or filter keys while deriving a new object type.

### 17. How can recursive conditional types hit compiler limits?

**Answer:** Deeply expanding recursive aliases can exceed instantiation depth or create slow checks; simplify recursion and bound input depth.

### 18. How do you type a function that accepts either sync or async callbacks?

**Answer:** Use a return union such as T | Promise<T> or PromiseLike<T>, then normalize with Promise.resolve at runtime.

### 19. What is Awaited<T> useful for?

**Answer:** It recursively models the value obtained by awaiting a Promise-like type.

### 20. How would you type a generic API client while keeping response inference?

**Answer:** Parameterize the route or decoder and infer the response from the declared endpoint contract rather than asserting arbitrary JSON.

## Advanced type design

### 21. Why is a generic type parameter sometimes unnecessary?

**Answer:** If it appears only once and does not relate multiple positions, it may add complexity without improving inference.

### 22. How do overload implementation signatures work?

**Answer:** Only overload signatures are visible to callers; the implementation must be broad enough to handle all declared forms.

### 23. What is declaration emit?

**Answer:** The compiler writes .d.ts files describing a library’s public API so downstream TypeScript consumers can type-check.

### 24. Why should public library types avoid exposing internal implementation details?

**Answer:** Internal types can become accidental compatibility commitments; export a deliberately small, stable contract.

### 25. How do project references help a large TypeScript repository?

**Answer:** They divide compilation into dependency projects, enable incremental builds, and support clearer package boundaries.

### 26. What is the trade-off of skipLibCheck?

**Answer:** It can speed builds by skipping checks inside declaration files, but may hide incompatible or broken type packages.

### 27. How can module augmentation be used safely?

**Answer:** Augment a known module in a declaration file and ensure the augmentation is included; global changes should be tightly scoped.

### 28. What is the difference between type and runtime enums?

**Answer:** Enums may emit JavaScript objects; string unions and const objects can offer type constraints with different runtime output.

### 29. How would you type JSON.parse safely?

**Answer:** Treat its result as unknown, then validate the shape before using it as a domain type.

### 30. Why is a type assertion at an HTTP boundary risky?

**Answer:** Network payloads are not checked by the compiler; an incorrect assertion can spread invalid assumptions through the app.

## Type system edge cases

### 31. How can a schema validator complement TypeScript?

**Answer:** A runtime schema checks actual values and can infer or share a static type, reducing duplicated contracts.

### 32. What does exactOptionalPropertyTypes change?

**Answer:** It distinguishes an absent optional property from a present property whose value is undefined in certain assignments.

### 33. What does noUncheckedIndexedAccess change?

**Answer:** It adds undefined to many indexed property and array access results, forcing code to consider missing keys or positions.

### 34. How would you design a safe dictionary type?

**Answer:** Use Record for known key unions; for open dictionaries, account for missing keys and consider Map for runtime behavior.

### 35. What is the difference between interface extension and intersection?

**Answer:** extends reports incompatible property declarations clearly; intersections combine constraints and can produce impossible property types.

### 36. How can recursive JSON values be represented?

**Answer:** Use a recursive union of string, number, boolean, null, arrays, and string-keyed objects, subject to JSON constraints.

### 37. Why can an assertion function be more useful than a boolean guard?

**Answer:** It can throw on invalid data while narrowing the value for all subsequent statements in the caller.

### 38. What is a const generic parameter?

**Answer:** It asks inference to preserve literal-like structure for a generic argument, useful for configuration APIs with exact tuples.

### 39. How do you keep a type-safe key/value setter?

**Answer:** Use key: keyof T and value: T[K], tying the accepted value type to the selected property.

### 40. What is a phantom type parameter?

**Answer:** It distinguishes otherwise identical types at compile time without storing a corresponding runtime field.

## References

- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- [Everyday Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)
- [Narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)
- [Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
- [Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html)
