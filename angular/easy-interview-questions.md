# Angular: Easy Interview Questions and Answers

**40 questions.** These answers are short enough to rehearse and specific enough to expand with an example from a real application.

## Components and templates

### 1. What is Angular?

**Answer:** Angular is a TypeScript-based framework for building web applications with components, templates, dependency injection, routing, forms, and HTTP support.

### 2. What is an Angular component?

**Answer:** A component combines a TypeScript class with a template and metadata that describe its selector, styles, and behavior.

### 3. What does the Component decorator do?

**Answer:** It supplies metadata Angular uses to compile and render a class as a component.

### 4. What is a component template?

**Answer:** It is the HTML-like view that displays data and handles user interaction for the component.

### 5. What is a selector?

**Answer:** A selector is the element or attribute name used to place a component or directive in a template.

### 6. What is a standalone component?

**Answer:** It is a component that can declare its template dependencies directly and be imported without being declared in an NgModule.

### 7. What is an Angular template expression?

**Answer:** It is a restricted expression used in a template binding to read component data and calculate display values.

### 8. What is interpolation?

**Answer:** Interpolation displays a value in text, commonly written with double curly braces around an expression.

### 9. What is property binding?

**Answer:** It sets a DOM property or directive input from a component expression, commonly written with square brackets.

### 10. What is event binding?

**Answer:** It listens for a DOM or component event and calls an expression, written with parentheses around the event name.

## Signals and dependency injection

### 11. What is two-way binding?

**Answer:** It keeps a view value and component value in sync, traditionally using the banana-in-a-box syntax.

### 12. What is a signal in Angular?

**Answer:** A signal is a reactive value that notifies Angular when its value changes and consumers need updating.

### 13. How do you read a signal?

**Answer:** Call the signal like a function, for example count().

### 14. How do you update a writable signal?

**Answer:** Use set with a new value or update with a function based on the current value.

### 15. What is a computed signal?

**Answer:** It derives a value from other signals and recalculates lazily when its dependencies change.

### 16. What is an Angular service?

**Answer:** A class or value that provides reusable behavior or data outside a component’s presentation.

### 17. What is dependency injection?

**Answer:** It lets a class request dependencies from an injector instead of creating those objects itself.

### 18. How can a component inject a service?

**Answer:** Use constructor injection or Angular’s inject function in a supported injection context.

### 19. What does providedIn: root mean on an injectable service?

**Answer:** It makes the service available from the application root injector and allows tree-shaking when unused.

### 20. What is a directive?

**Answer:** A directive adds behavior to an existing element or changes how a portion of a template is rendered.

## Forms routing and HTTP

### 21. What is a pipe?

**Answer:** A pipe transforms a value for display in a template, such as formatting a date or currency.

### 22. What does the async pipe do?

**Answer:** It subscribes to an Observable or Promise in a template, exposes its latest value, and cleans up its subscription.

### 23. How does a parent pass data to a child component?

**Answer:** Bind a value to a child input, using a signal input or a supported input property.

### 24. How can a child notify its parent?

**Answer:** Expose an output and emit an event that the parent handles in its template.

### 25. What is Angular routing?

**Answer:** The router maps URL paths to views and supports navigation, parameters, guards, and lazy-loaded features.

### 26. What is a route parameter?

**Answer:** It is a dynamic part of a URL path, such as an item id, read through the router’s activated route state.

### 27. What is a lazy-loaded route?

**Answer:** It loads a feature’s code only when navigation reaches that route, reducing the initial bundle.

### 28. What is an Angular form?

**Answer:** It models user input, validation state, and submission behavior in a template-driven or reactive approach.

### 29. What is a reactive form?

**Answer:** It defines controls and validators in TypeScript, giving the component explicit access to form state.

### 30. What is a template-driven form?

**Answer:** It relies more on template directives and two-way binding to create and manage form controls.

## Production patterns and edge cases

### 31. What is HttpClient?

**Answer:** It is Angular’s injectable API for making typed HTTP requests and handling their results as Observables.

### 32. What is an HTTP interceptor?

**Answer:** It is a function or class in the HTTP pipeline that can inspect or modify outgoing requests and incoming responses.

### 33. What does ngOnInit do?

**Answer:** It is a lifecycle hook called after Angular initializes the component’s input properties.

### 34. What does ngOnDestroy do?

**Answer:** It is a lifecycle hook for cleanup before a component or directive is destroyed.

### 35. What is change detection?

**Answer:** It is the process Angular uses to check bindings and update the rendered view when application state changes.

### 36. What is Angular CLI?

**Answer:** It is the command-line tool for creating, building, serving, testing, and maintaining Angular applications.

### 37. What is an Angular environment configuration?

**Answer:** It is build or runtime configuration such as API URLs and feature settings; secrets must not be placed in public frontend bundles.

### 38. What does *ngFor traditionally do?

**Answer:** It repeats a template for each item in a collection; current templates can also use Angular’s built-in @for control flow.

### 39. What does *ngIf traditionally do?

**Answer:** It conditionally includes a template; current Angular templates can also use built-in @if control flow.

### 40. What is a route guard?

**Answer:** It is a router hook that can allow, redirect, or prevent navigation based on application logic.

## References

- [Angular components](https://angular.dev/guide/components)
- [Angular templates](https://angular.dev/guide/templates)
- [Angular signals](https://angular.dev/guide/signals)
- [Angular dependency injection](https://angular.dev/guide/di)
- [Angular forms](https://angular.dev/guide/forms)
- [Angular routing](https://angular.dev/guide/routing)
- [Angular HTTP](https://angular.dev/guide/http)
