# RxJS and Reactive Programming: Medium Interview Questions and Answers

**40 questions.** These answers are short enough to rehearse and specific enough to expand with an example from a real application.

## Observable foundations

### 1. How is an Observable different from a Promise?

**Answer:** A Promise represents one eventual result; an Observable can emit zero or many values and supports operators and cancellation by unsubscribe.

### 2. Are Observables lazy?

**Answer:** Many are lazy: the producer starts on subscription, though a particular Observable can wrap an already-running source.

### 3. What is the difference between cold and hot HTTP Observables in Angular?

**Answer:** HttpClient Observables are cold; each subscription typically issues a request unless the result is deliberately shared.

### 4. Why can subscribing twice issue two HTTP requests?

**Answer:** Each subscription to a cold HttpClient Observable starts an independent execution.

### 5. How does switchMap solve a typeahead search?

**Answer:** It switches to the newest request Observable, unsubscribing from the prior one when a new query arrives.

### 6. When is switchMap a poor choice?

**Answer:** When every operation must finish, such as recording each payment or ordered write; cancellation may discard prior work.

### 7. When would you choose mergeMap?

**Answer:** When independent inner operations can run concurrently and each source value must be handled.

### 8. When would you choose concatMap?

**Answer:** When every operation must be processed sequentially in input order, such as queued writes.

### 9. When would you choose exhaustMap?

**Answer:** When repeated triggers should be ignored while one operation is active, such as preventing repeated login submits.

### 10. What is the concurrency parameter on mergeMap for?

**Answer:** It limits the number of active inner subscriptions so a large stream does not start unbounded work.

## Mapping and combining streams

### 11. How does debounceTime differ from auditTime?

**Answer:** debounceTime waits for silence and emits the latest value; auditTime emits the latest value at the end of fixed windows.

### 12. How does throttleTime differ from debounceTime?

**Answer:** throttleTime limits emissions within a window; debounceTime waits until events stop arriving.

### 13. How does distinctUntilChanged compare objects by default?

**Answer:** It uses strict equality by default, so new object references with identical fields are considered different.

### 14. How can you compare search form values with distinctUntilChanged?

**Answer:** Provide a comparator or map to a stable key so equivalent values are treated as equal.

### 15. How should catchError be placed in a search stream?

**Answer:** Place it inside the switched request stream when one request failure should not terminate future search input.

### 16. What happens if catchError is placed outside the whole source chain?

**Answer:** A handled error may replace and complete the outer stream, preventing future user input from being processed.

### 17. What does retry do?

**Answer:** It resubscribes to a failed source a configured number of times; use it only when repeated execution is safe.

### 18. Why can retry cause duplicate writes?

**Answer:** It resubmits the source operation after error; the server might have processed the first request despite a lost response.

### 19. What does retryWhen traditionally support?

**Answer:** It lets retry timing or policy be controlled by a notifier stream; modern RxJS also provides configurable retry patterns.

### 20. What is shareReplay useful for?

**Answer:** It can share one source execution and replay recent values to later subscribers, often caching a completed lookup result.

## Errors sharing and lifecycle

### 21. What risk comes with shareReplay?

**Answer:** Values or active subscriptions may be retained longer than expected; configure reset behavior and lifecycle for the intended cache.

### 22. How do combineLatest and forkJoin differ?

**Answer:** combineLatest emits whenever inputs change after each has emitted; forkJoin emits once after all inputs complete.

### 23. Why might forkJoin never emit?

**Answer:** An input may never complete; forkJoin waits for every input to complete.

### 24. What happens if one forkJoin input errors?

**Answer:** The combined Observable errors and unsubscribes other inputs unless the error is caught within that input.

### 25. How do you combine user input with an API request?

**Answer:** Map the input stream to a request and use a higher-order operator such as switchMap with appropriate debounce and error handling.

### 26. What is the difference between Subject and BehaviorSubject?

**Answer:** Subject gives new subscribers only future emissions; BehaviorSubject immediately gives its current value.

### 27. When might ReplaySubject be useful?

**Answer:** When late subscribers need a limited history, such as the most recent state transitions.

### 28. Why is a large ReplaySubject buffer risky?

**Answer:** It retains values and their referenced object graphs, increasing memory use and possibly exposing stale data.

### 29. What does share do?

**Answer:** It multicasts a source among concurrent subscribers, generally without replaying values to late subscribers.

### 30. How is shareReplay different from share?

**Answer:** shareReplay also replays buffered notifications according to its configuration; this can serve cached results.

## Cancellation and operator edge cases

### 31. What does materialize do conceptually?

**Answer:** It turns next, error, and complete notifications into ordinary Notification values for stream processing.

### 32. What is backpressure?

**Answer:** It is the need to regulate producers when consumers cannot keep up; JavaScript Observables do not automatically provide general backpressure.

### 33. How can bufferTime help?

**Answer:** It groups emissions received during time windows into arrays for batch processing.

### 34. What does windowTime produce?

**Answer:** It emits inner Observables representing successive time windows of the source.

### 35. How do you make a timer stop when a component is destroyed?

**Answer:** Unsubscribe through the async pipe, takeUntilDestroyed, or explicit cleanup tied to the component lifetime.

### 36. What does takeUntil do?

**Answer:** It mirrors a source until a notifier emits, then completes and unsubscribes from the source.

### 37. Why is a destroyed notifier often paired with takeUntil?

**Answer:** It ends owned subscriptions when a component is destroyed, though Angular now offers takeUntilDestroyed for this pattern.

### 38. How does finalize behave on cancellation?

**Answer:** It runs on completion, error, and explicit unsubscription, so it is useful for clearing loading state.

### 39. Does map handle asynchronous callbacks automatically?

**Answer:** No. It emits the returned Promise as a value; use a higher-order mapping operator to flatten async work.

### 40. What is flattening an Observable of Observables?

**Answer:** It means subscribing to inner streams and forwarding their values according to a strategy like switch, merge, concat, or exhaust.

## References

- [RxJS Observable guide](https://rxjs.dev/guide/observable)
- [RxJS operators guide](https://rxjs.dev/guide/operators)
- [RxJS higher-order Observables](https://rxjs.dev/guide/higher-order-observables)
- [RxJS error handling](https://rxjs.dev/guide/error_handling)
- [Angular RxJS interop](https://angular.dev/ecosystem/rxjs-interop)
