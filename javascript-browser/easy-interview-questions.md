# JavaScript and Browser Fundamentals: Easy Interview Questions and Answers

**40 questions.** These answers are short enough to rehearse and specific enough to expand with an example from a real application.

## Language foundations

### 1. What is JavaScript used for in a browser?

**Answer:** It adds behavior to web pages, handles user events, updates the DOM, and communicates with web APIs and servers.

### 2. How do you declare a variable that can be reassigned?

**Answer:** Use let, for example let count = 0; then assign a new value with count = 1.

### 3. When should you use const?

**Answer:** Use const when the binding should not be reassigned. Objects declared const can still have mutable properties.

### 4. What is the difference between let and var?

**Answer:** let is block-scoped; var is function-scoped and has older hoisting behavior that can surprise readers.

### 5. What is a string?

**Answer:** A string is an immutable sequence of text values, commonly written with single, double, or backtick quotes.

### 6. What is a Boolean?

**Answer:** A Boolean has one of two values: true or false.

### 7. What is an array?

**Answer:** An array is an ordered, indexed collection, for example colors = [blue, green].

### 8. What is an object literal?

**Answer:** It creates an object from key-value properties, such as name: Mira and active: true.

### 9. What does typeof return?

**Answer:** It returns a string describing a value’s type, though cases such as typeof null are historical quirks.

### 10. What is a function?

**Answer:** A reusable callable block of code that can accept parameters and return a value.

## DOM and events

### 11. What is an arrow function?

**Answer:** A concise function syntax that inherits this from its surrounding scope and cannot be called with new.

### 12. What does return do?

**Answer:** It ends the current function call and optionally provides a value to the caller.

### 13. What is a conditional statement?

**Answer:** if/else selects which code runs based on a truthy or falsy condition.

### 14. What is a loop?

**Answer:** A construct such as for or while that repeats work while its iteration condition allows.

### 15. What does strict equality compare?

**Answer:** It compares values without coercing their types, unlike loose equality.

### 16. What is null?

**Answer:** null is an intentional absence of an object-like value, distinct from undefined.

### 17. What does undefined usually mean?

**Answer:** A value is undefined when it has not been assigned, is absent, or a function does not return a value.

### 18. What is an event listener?

**Answer:** It registers a callback for an event, for example button.addEventListener with a click handler.

### 19. What is the DOM?

**Answer:** The Document Object Model is the browser’s object representation of a document, which scripts can inspect and update.

### 20. How do you select an element by id?

**Answer:** Use document.getElementById or document.querySelector with an id selector.

## Async work and APIs

### 21. How do you update an element’s text safely?

**Answer:** Set its textContent property; this treats the value as text rather than parsing it as HTML.

### 22. What is an event object?

**Answer:** It contains information about an event, such as its target, key, pointer position, or propagation controls.

### 23. What does preventDefault do?

**Answer:** It cancels a browser’s default action for a cancelable event, such as a form’s normal navigation on submit.

### 24. What does stopPropagation do?

**Answer:** It stops the current event from continuing through its propagation path; it does not cancel the default action.

### 25. What array method adds items to the end?

**Answer:** push adds one or more elements to the end and returns the new length.

### 26. What does map do on an array?

**Answer:** It creates a new array by applying a callback to each visited element.

### 27. What does filter do?

**Answer:** It creates a new array containing the elements for which the callback returns a truthy value.

### 28. What does find do?

**Answer:** It returns the first element that matches a condition, or undefined if none matches.

### 29. What is JSON?

**Answer:** JSON is a text data format used to exchange structured data; JSON.parse reads it and JSON.stringify writes it.

### 30. What is a Promise?

**Answer:** A Promise represents the eventual fulfillment or rejection of one asynchronous operation.

## Runtime behavior and gotchas

### 31. What does async do on a function?

**Answer:** It makes the function return a Promise; a returned value becomes its fulfillment value.

### 32. What does await do?

**Answer:** It pauses the surrounding async function until a Promise settles, then returns its value or throws its rejection.

### 33. What does fetch return?

**Answer:** fetch returns a Promise for a Response; check response.ok because HTTP error statuses do not usually reject it.

### 34. What is localStorage?

**Answer:** It stores string key-value data for an origin across browser sessions, subject to browser policy and storage limits.

### 35. What is sessionStorage?

**Answer:** It stores string key-value data for an origin in a page session, typically isolated by tab.

### 36. What is a module in JavaScript?

**Answer:** A module is a file with explicit imports and exports, providing scoped names and a dependency structure.

### 37. How do you export a named value?

**Answer:** Use export, such as export function formatDate(value), then import it by name.

### 38. What is a template literal?

**Answer:** A backtick-delimited string that supports interpolation, for example Hello followed by the value of name.

### 39. What does spread syntax do?

**Answer:** It expands iterable values or object properties in contexts such as function calls, array literals, and object literals.

### 40. What is optional chaining?

**Answer:** ?. safely stops a property or call chain when the left side is null or undefined.

## References

- [MDN JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide)
- [MDN promises guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)
- [MDN JavaScript execution model](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Event_loop)
- [MDN Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
