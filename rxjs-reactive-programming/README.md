# RxJS and Reactive Programming

Compose asynchronous work with Observables, higher-order mapping, sharing, cancellation, error handling, and Angular lifecycle cleanup.

## What you will practice

- Explain core concepts in plain language and connect them to a real UI feature.
- Choose APIs and patterns based on lifecycle, data flow, performance, and failure behavior.
- Recognize common interview traps and describe a practical debugging approach.

## Topic map

- [Observable foundations](easy-interview-questions.md#observable-foundations) — questions 1–10
- [Mapping and combining streams](easy-interview-questions.md#mapping-and-combining-streams) — questions 11–20
- [Errors sharing and lifecycle](easy-interview-questions.md#errors-sharing-and-lifecycle) — questions 21–30
- [Cancellation and operator edge cases](easy-interview-questions.md#cancellation-and-operator-edge-cases) — questions 31–40


## Quick example

Latest-query search stream:

```ts
terms.pipe(
  debounceTime(250),
  distinctUntilChanged(),
  switchMap(term => http.get<SearchResult>("/api/search", { params: { q: term } }))
);
```

## Question sets

- [Easy — 40 questions](easy-interview-questions.md)
- [Medium — 40 questions](medium-interview-questions.md)
- [Hard — 40 questions](hard-interview-questions.md)
- [Tricky — 40 questions](tricky-interview-questions.md)

## Study tip

Try each answer aloud in your own words. For scenario questions, state the user problem, your design choice, the trade-off, and how you would verify it.

## References

- [RxJS Observable guide](https://rxjs.dev/guide/observable)
- [RxJS operators guide](https://rxjs.dev/guide/operators)
- [RxJS higher-order Observables](https://rxjs.dev/guide/higher-order-observables)
- [RxJS error handling](https://rxjs.dev/guide/error_handling)
- [Angular RxJS interop](https://angular.dev/ecosystem/rxjs-interop)
