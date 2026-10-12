# RxJS and Reactive Programming: Tricky Interview Questions and Answers

**40 questions.** These answers are short enough to rehearse and specific enough to expand with an example from a real application.

## Observable foundations

### 1. Does creating an Observable start its producer?

**Answer:** Usually no; subscription starts it, though an Observable wrapping an already-running source can behave differently.

### 2. Does subscribe return the emitted value?

**Answer:** No. It returns a Subscription; emitted values arrive through observer callbacks.

### 3. Can an Observable complete and then emit a value later?

**Answer:** No. Complete and error are terminal notifications for that subscription.

### 4. Can an Observable call both error and complete?

**Answer:** No. A subscription has at most one terminal notification.

### 5. Does unsubscribe cause the source to send complete?

**Answer:** No. It stops that subscriber; completion is a source notification, not an automatic effect of unsubscribe.

### 6. Does switchMap cancel the server’s business operation?

**Answer:** No. It unsubscribes the client request where supported; the server may already have received and processed it.

### 7. Does switchMap guarantee the latest request completes?

**Answer:** It keeps the latest inner subscription, but that request may fail, be canceled by a later value, or never complete.

### 8. Will mergeMap preserve output order?

**Answer:** Not necessarily. Concurrent inner streams emit as they produce values, which can differ from source order.

### 9. Does concatMap run all requests at once?

**Answer:** No. It subscribes to each inner Observable sequentially after the prior one completes.

### 10. Does exhaustMap queue ignored clicks?

**Answer:** No. Clicks during an active inner stream are dropped, not queued for later execution.

## Mapping and combining streams

### 11. Does debounceTime cancel a network request already started?

**Answer:** No. It delays emissions; use a switching operator or cancellation mechanism for in-flight work.

### 12. Does distinctUntilChanged compare object contents?

**Answer:** No. It uses its comparator, which defaults to strict equality and therefore compares object identity.

### 13. Does catchError resume the failed source automatically?

**Answer:** No. It replaces the errored stream with the Observable you return; it does not restart unless you return a retrying source.

### 14. Can catchError placement change whether a search stream keeps working?

**Answer:** Yes. Catching inside the request branch can preserve the input stream; catching outside may replace and complete it.

### 15. Does retry retry only network failures?

**Answer:** No. It resubscribes after any source error unless a predicate or delay policy filters errors.

### 16. Is retry safe for every HTTP request?

**Answer:** No. Retrying a write can duplicate effects if the server processed the first attempt.

### 17. Does retryWhen delay a retry by itself?

**Answer:** No. Its notifier must control when and whether resubscription occurs.

### 18. Will finalize run only on success?

**Answer:** No. It runs after completion, error, or unsubscription.

### 19. Does finalize receive the final value or error?

**Answer:** No. It is a cleanup callback without the outcome value; use tap or catchError when inspecting notifications.

### 20. Does forkJoin work like Promise.all on streams?

**Answer:** Only for finite sources: it waits for all to complete and emits their last values; a never-ending input prevents output.

## Errors sharing and lifecycle

### 21. Does combineLatest emit before every input has emitted once?

**Answer:** No. It needs an initial value from each input first.

### 22. Will combineLatest emit again after an input completes?

**Answer:** It can continue using that input’s last value while other inputs emit, unless completion rules end the combination.

### 23. Does shareReplay always reset its cache when the last subscriber leaves?

**Answer:** No. Reset behavior depends on configuration and RxJS version; specify and test the intended lifetime.

### 24. Does shareReplay make a source permanently cached?

**Answer:** Not inherently. Source completion and reset settings affect replay lifetime; keep a handle to clear application caches.

### 25. Does Subject remember the last value for late subscribers?

**Answer:** No. Use BehaviorSubject or ReplaySubject when late subscribers need a current or previous value.

### 26. Does BehaviorSubject emit its initial value to each new subscriber?

**Answer:** Yes, while it is active; after termination, terminal behavior applies.

### 27. Can ReplaySubject cause stale data to be shown?

**Answer:** Yes. It deliberately replays buffered values; bound the buffer and clear or replace the subject when state changes.

### 28. Does map flatten a Promise returned from its callback?

**Answer:** No. It emits the Promise as a value; use a flattening operator when the Promise result should become stream values.

### 29. Does an RxJS Observable automatically apply backpressure?

**Answer:** No. Manage producer rate, buffering, sampling, or concurrency explicitly.

### 30. Does takeUntil unsubscribe when the notifier completes without a value?

**Answer:** No. It reacts to a notifier emission; completion alone does not trigger the stop.

## Cancellation and operator edge cases

### 31. Can takeUntilDestroyed clean up work not owned by the Angular context?

**Answer:** It ends the composed subscription at that context’s destruction; external side effects still need correct teardown.

### 32. Does converting from a Promise make the Promise cancellable?

**Answer:** No. Unsubscription can prevent delivery, but ordinary Promise work continues.

### 33. Can multiple subscriptions share one cold Observable execution?

**Answer:** Not by default. Each subscription can execute the producer separately; use a sharing operator when appropriate.

### 34. Does a Subject guarantee events are delivered to future subscribers?

**Answer:** No. It multicasts to current subscribers unless a replaying variant is used.

### 35. Will every inner stream in mergeMap finish before the outer stream completes?

**Answer:** Usually the result completes only after source completion and active inner streams finish, but errors can terminate it earlier.

### 36. Does switchMap cancel an inner stream when the outer completes?

**Answer:** No. It normally lets the current inner complete; a new outer value is what replaces the prior inner.

### 37. Can exhaustMap be used to preserve every payment request?

**Answer:** No. It drops triggers while busy; payment workflows need an explicit queue and server-side idempotency.

### 38. Is an empty Observable the same as an Observable that emits undefined?

**Answer:** No. Empty completes without a next value; of(undefined) emits one undefined value and then completes.

### 39. Does defaultIfEmpty catch an error?

**Answer:** No. It emits a fallback only if a source completes without a value; use catchError for errors.

### 40. Can finalize clear a loading flag if a subscription is never torn down?

**Answer:** No. A never-ending active stream keeps running; ensure a completion, cancellation, timeout, or lifecycle teardown.

## References

- [RxJS Observable guide](https://rxjs.dev/guide/observable)
- [RxJS operators guide](https://rxjs.dev/guide/operators)
- [RxJS higher-order Observables](https://rxjs.dev/guide/higher-order-observables)
- [RxJS error handling](https://rxjs.dev/guide/error_handling)
- [Angular RxJS interop](https://angular.dev/ecosystem/rxjs-interop)
