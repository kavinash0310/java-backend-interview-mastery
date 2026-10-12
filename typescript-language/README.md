# TypeScript

Practice everyday typing, narrowing, generics, utility types, module setup, and safe boundaries around runtime data.

## What you will practice

- Explain core concepts in plain language and connect them to a real UI feature.
- Choose APIs and patterns based on lifecycle, data flow, performance, and failure behavior.
- Recognize common interview traps and describe a practical debugging approach.

## Topic map

- [Types and everyday syntax](easy-interview-questions.md#types-and-everyday-syntax) — questions 1–10
- [Narrowing and generics](easy-interview-questions.md#narrowing-and-generics) — questions 11–20
- [Advanced type design](easy-interview-questions.md#advanced-type-design) — questions 21–30
- [Type system edge cases](easy-interview-questions.md#type-system-edge-cases) — questions 31–40


## Quick example

Narrowing untrusted data:

```ts
type SearchResult =
  | { kind: "success"; items: string[] }
  | { kind: "failure"; message: string };

function describe(result: SearchResult): string {
  return result.kind === "success"
    ? result.items.join(", ")
    : result.message;
}
```

## Question sets

- [Easy — 40 questions](easy-interview-questions.md)
- [Medium — 40 questions](medium-interview-questions.md)
- [Hard — 40 questions](hard-interview-questions.md)
- [Tricky — 40 questions](tricky-interview-questions.md)

## Study tip

Try each answer aloud in your own words. For scenario questions, state the user problem, your design choice, the trade-off, and how you would verify it.

## References

- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- [Everyday Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)
- [Narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)
- [Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
- [Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html)
