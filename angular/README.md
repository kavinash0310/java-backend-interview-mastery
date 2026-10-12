# Angular

Build maintainable Angular applications with components, templates, signals, dependency injection, routing, forms, HTTP, and SSR-aware practices.

## What you will practice

- Explain core concepts in plain language and connect them to a real UI feature.
- Choose APIs and patterns based on lifecycle, data flow, performance, and failure behavior.
- Recognize common interview traps and describe a practical debugging approach.

## Topic map

- [Components and templates](easy-interview-questions.md#components-and-templates) — questions 1–10
- [Signals and dependency injection](easy-interview-questions.md#signals-and-dependency-injection) — questions 11–20
- [Forms routing and HTTP](easy-interview-questions.md#forms-routing-and-http) — questions 21–30
- [Production patterns and edge cases](easy-interview-questions.md#production-patterns-and-edge-cases) — questions 31–40


## Quick example

Signal-driven component:

```ts
import { Component, signal } from '@angular/core';

@Component({
  selector: 'app-counter',
  standalone: true,
  template: '<button type="button" (click)="count.update(n => n + 1)">Count: {{ count() }}</button>'
})
export class CounterComponent {
  readonly count = signal(0);
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

- [Angular components](https://angular.dev/guide/components)
- [Angular templates](https://angular.dev/guide/templates)
- [Angular signals](https://angular.dev/guide/signals)
- [Angular dependency injection](https://angular.dev/guide/di)
- [Angular forms](https://angular.dev/guide/forms)
- [Angular routing](https://angular.dev/guide/routing)
- [Angular HTTP](https://angular.dev/guide/http)
