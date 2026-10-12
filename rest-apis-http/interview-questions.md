# Reactive API Client Interview Questions

These questions cover the client side of a backend API, especially when newer input should replace an older in-flight read.

## A stock quote lookup changes as the user selects symbols. Promise or Observable, and how do you avoid stale results?

A Promise is fine for one request that produces one result and does not need cancellation or stream composition. In Angular, `HttpClient` returns a cold Observable; it starts on subscription and unsubscribing aborts the in-flight client request. An Observable is also the natural model for a live quote feed that emits repeatedly.

For a search box or changing ticker, use `switchMap` so only the newest request is observed. A typeahead can also debounce keystrokes and skip identical values:

~~~typescript
import { debounceTime, distinctUntilChanged, switchMap } from 'rxjs';

// Assume symbols$ emits the current search term and http is Angular HttpClient.
const quotes$ = symbols$.pipe(
  debounceTime(250),
  distinctUntilChanged(),
  switchMap(symbol =>
    http.get<Quote[]>('/api/quotes?symbol=' + encodeURIComponent(symbol))
  )
);
~~~

When a new symbol arrives, `switchMap` unsubscribes from the previous inner Observable. With Angular `HttpClient`, that aborts the client request, but it cannot guarantee that a server which already received the request stops its work. This pattern is for replaceable reads; write endpoints still need normal idempotency and cancellation semantics.

See the [Angular HTTP guide](https://angular.dev/guide/http/making-requests) and the [RxJS `switchMap` API](https://rxjs.dev/api/index/function/switchMap).
