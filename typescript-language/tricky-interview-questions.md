# TypeScript: Tricky Interview Questions and Answers

**40 questions.** These answers are short enough to rehearse and specific enough to expand with an example from a real application.

## Types and everyday syntax

### 1. Does TypeScript type-check JavaScript at runtime?

**Answer:** No. Types are erased; runtime values need actual validation if they come from users, APIs, storage, or other untrusted sources.

### 2. Does any mean the value can safely be used as every type?

**Answer:** The compiler permits it, but that is an unchecked escape hatch; a wrong assumption can fail at runtime.

### 3. Does unknown accept arbitrary operations?

**Answer:** No. You must narrow it or assert a type before accessing members or calling it.

### 4. Is never the same as void?

**Answer:** No. void means a function has no useful return value; never means it cannot complete normally.

### 5. Can an interface describe a union type?

**Answer:** Not directly. Use a type alias for a union; interfaces describe object shapes.

### 6. Does a type alias create a new runtime type?

**Answer:** No. Type aliases disappear during compilation and do not create runtime constructors or validation.

### 7. Does readonly make an object immutable at runtime?

**Answer:** No. It restricts assignments through TypeScript’s type system but does not freeze the JavaScript object.

### 8. Does a private modifier always hide a class property at runtime?

**Answer:** No. TypeScript private is generally compile-time only; ECMAScript #private fields enforce runtime privacy.

### 9. Does as SomeType convert a value at runtime?

**Answer:** No. A type assertion is erased and does not convert or validate the value.

### 10. Can an interface accept a structurally compatible object without implements?

**Answer:** Yes. Compatibility is usually structural, so the declaration need not explicitly say implements.

## Narrowing and generics

### 11. Can an object literal trigger an error even when it has the required fields?

**Answer:** Yes. Excess property checks can flag extra literal properties that may indicate a typo.

### 12. Is a value typed as string | undefined safe to call toUpperCase on?

**Answer:** No. Check for undefined first or provide a fallback before using string methods.

### 13. Does the question mark in property?: T mean the value is exactly undefined?

**Answer:** It means the property may be absent; exactOptionalPropertyTypes further distinguishes absence from an explicit undefined value.

### 14. Does a generic T guarantee a particular property exists?

**Answer:** No. Add a constraint or narrow the value before accessing that property.

### 15. Can you construct a generic class’s static member using its type parameter?

**Answer:** Not directly; a class’s static side cannot refer to its generic instance type parameter.

### 16. Does ReturnType work on every overloaded function as a union of all returns?

**Answer:** Not necessarily; it uses the last overload signature for inference in common cases.

### 17. Does keyof an object type always produce string?

**Answer:** No. It produces a union of property key types, which may include string, number, or symbol keys.

### 18. Can keyof string index signatures be only strings?

**Answer:** A string index signature can yield string | number because JavaScript coerces numeric object keys to strings.

### 19. Does satisfies widen every literal to its target type?

**Answer:** No. It checks compatibility while generally preserving the expression’s more specific inferred type.

### 20. Does as const freeze the value?

**Answer:** No. It creates readonly literal types at compile time; it does not freeze the object at runtime.

## Advanced type design

### 21. Is a type predicate guaranteed to be truthful?

**Answer:** No. The compiler trusts the predicate’s declaration; a faulty implementation can lie to callers.

### 22. Can typeof narrow every JavaScript object shape?

**Answer:** No. It distinguishes primitive categories and functions; use property checks or schema validation for object shapes.

### 23. Does instanceof work for every value received from an API?

**Answer:** No. JSON data has no class prototype identity; validate its object shape instead.

### 24. Will an enum always emit no JavaScript?

**Answer:** No. Regular enums usually emit runtime code; const enums and other representations have different build behavior.

### 25. Can two interfaces with the same name merge?

**Answer:** Yes, in the same declaration scope; this can be useful for augmentation but can also change a public type unexpectedly.

### 26. Does import type exist in emitted JavaScript?

**Answer:** No. It is for type checking only and is erased from emitted output.

### 27. Can strict mode catch incorrect network payloads?

**Answer:** No. It catches source-level type issues, not whether external data matches a declared shape at runtime.

### 28. Does noEmit mean TypeScript does no checking?

**Answer:** No. It can still perform type checking while another tool handles JavaScript output.

### 29. Does target set the browser’s supported API list?

**Answer:** No. It controls emitted syntax level; it does not polyfill runtime APIs such as fetch.

### 30. Does Partial<T> make nested object properties optional too?

**Answer:** No. It is shallow unless combined with a separately defined recursive transformation.

## Type system edge cases

### 31. Does readonly array mean no one can mutate the underlying array?

**Answer:** No. It prevents mutation through that reference, but a mutable alias may still change it.

### 32. Will an optional function parameter always have a default at runtime?

**Answer:** No. It may be undefined; only a default initializer supplies a fallback.

### 33. Can a function claim to return string but fall off without returning?

**Answer:** The checker reports a mismatch when return checking is enabled; runtime JavaScript would produce undefined.

### 34. Does a union type imply the value has only one listed type forever?

**Answer:** It describes possible values at a point; assignments and runtime changes can alter which member is present.

### 35. Can an intersection of incompatible property types become impossible?

**Answer:** Yes. For example string & number is never, making the resulting property unusable.

### 36. Can a type assertion from unknown bypass all type errors?

**Answer:** It can make the compiler accept an assumption, but it adds no runtime safety.

### 37. Does function parameter optionality mean it can be passed explicitly as undefined?

**Answer:** Usually yes unless stricter optional-property semantics or a different signature changes the contract.

### 38. Can a variable declared const be narrowed to a literal type?

**Answer:** Often yes when initialized with a literal; mutable let variables are generally widened.

### 39. Can a declaration file contain executable implementation?

**Answer:** No. A declaration file describes types and APIs; it is not the runtime implementation.

### 40. Is a generic parameter useful if it appears only in a return type?

**Answer:** It can let callers choose a type without evidence, creating unsafe results; derive it from inputs or a decoder instead.

## References

- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- [Everyday Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)
- [Narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)
- [Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
- [Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html)
