# RxJS and Reactive Programming: Easy Interview Questions and Answers

**40 questions.** These answers are short enough to rehearse and specific enough to expand with an example from a real application.

## Observable foundations

### 1. What is RxJS?

**Answer:** RxJS is a JavaScript library for composing asynchronous and event-based work with Observables and operators.

### 2. What is an Observable?

**Answer:** It represents a stream that can emit values over time, complete, or report an error.

### 3. What is an Observer?

**Answer:** It is the consumer with callbacks for next values, errors, and completion.

### 4. How do you subscribe to an Observable?

**Answer:** Call subscribe with callbacks or an observer object; the returned Subscription can be unsubscribed.

### 5. What is a Subscription?

**Answer:** It represents an active Observable execution and provides unsubscribe to stop work and release resources.

### 6. What does of do?

**Answer:** of creates an Observable that emits the provided values and then completes.

### 7. What does from do?

**Answer:** from converts supported inputs such as arrays, iterables, and Promises into an Observable.

### 8. What does map do in RxJS?

**Answer:** It transforms each emitted value into another value while preserving the stream shape.

### 9. What does filter do?

**Answer:** It forwards only values that satisfy a predicate.

### 10. What does tap do?

**Answer:** It runs a side effect for each notification without changing the notification value.

## Mapping and combining streams

### 11. What does take do?

**Answer:** It forwards a fixed number of values and then completes.

### 12. What does first do?

**Answer:** It emits the first matching value and completes, or errors if no value matches and no default is supplied.

### 13. What does catchError do?

**Answer:** It handles an error by switching to a replacement Observable or returning a different Observable.

### 14. What does finalize do?

**Answer:** It runs a callback when the Observable terminates or is unsubscribed, useful for cleanup.

### 15. What does debounceTime do?

**Answer:** It waits for a quiet interval and emits the latest value, commonly used for search input.

### 16. What does distinctUntilChanged do?

**Answer:** It suppresses consecutive values considered equal by its comparator.

### 17. What does switchMap do?

**Answer:** It maps each source value to an inner Observable and switches to the newest one, unsubscribing from the previous inner stream.

### 18. What does mergeMap do?

**Answer:** It maps values to inner Observables and subscribes to them concurrently.

### 19. What does concatMap do?

**Answer:** It maps values to inner Observables and processes them sequentially in source order.

### 20. What does exhaustMap do?

**Answer:** It starts an inner Observable only when no previous inner one is active, ignoring new source values meanwhile.

## Errors sharing and lifecycle

### 21. What does combineLatest do?

**Answer:** It emits using the latest value from each input after every input has emitted at least once.

### 22. What does forkJoin do?

**Answer:** It waits for all input Observables to complete, then emits their final values together.

### 23. What is a Subject?

**Answer:** It is both an Observable and an Observer that multicasts values to current subscribers.

### 24. What is a BehaviorSubject?

**Answer:** It stores a current value and immediately emits that value to each new subscriber.

### 25. What is a ReplaySubject?

**Answer:** It replays a configured number or time window of previous values to new subscribers.

### 26. What is a cold Observable?

**Answer:** Each subscription typically starts an independent execution, such as a new HTTP request.

### 27. What is a hot Observable?

**Answer:** Values are produced independently of a particular subscriber, so late subscribers may miss earlier values.

### 28. What does multicast mean?

**Answer:** One producer delivers notifications to multiple observers rather than starting separate executions for each.

### 29. What is a higher-order Observable?

**Answer:** It is an Observable that emits other Observables, such as a search stream that creates requests.

### 30. What does pipe do?

**Answer:** It composes operators into a readable transformation chain without manually nesting calls.

## Cancellation and operator edge cases

### 31. What is the difference between next and complete?

**Answer:** next delivers a value; complete signals that no more values will arrive.

### 32. Can an Observable emit values after error?

**Answer:** No. Error is a terminal notification; a replacement stream is needed for continued work.

### 33. Can an Observable complete without emitting?

**Answer:** Yes. Completion can occur with zero values; consumers must handle that case.

### 34. Why should subscriptions be cleaned up?

**Answer:** Long-lived streams can keep callbacks and component state alive after a view is gone.

### 35. What does unsubscribe do?

**Answer:** It stops that subscription’s execution and invokes teardown logic where supported.

### 36. What is an RxJS operator?

**Answer:** It is a function that returns a new Observable with transformed, filtered, combined, or controlled behavior.

### 37. What does interval do?

**Answer:** It creates an Observable that emits increasing numbers on a time interval until unsubscribed.

### 38. What does timer do?

**Answer:** It emits after a delay and can optionally continue with a period.

### 39. What is the async pipe’s relationship to RxJS?

**Answer:** Angular’s async pipe subscribes to an Observable in a template and releases the subscription when its view is destroyed.

### 40. What is an error channel?

**Answer:** It is the Observable notification path used to signal a terminal failure, separate from ordinary next values.

## References

- [RxJS Observable guide](https://rxjs.dev/guide/observable)
- [RxJS operators guide](https://rxjs.dev/guide/operators)
- [RxJS higher-order Observables](https://rxjs.dev/guide/higher-order-observables)
- [RxJS error handling](https://rxjs.dev/guide/error_handling)
- [Angular RxJS interop](https://angular.dev/ecosystem/rxjs-interop)
