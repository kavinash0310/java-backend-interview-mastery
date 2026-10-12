# JavaScript and Browser Fundamentals

Understand JavaScript values, functions, modules, asynchronous execution, DOM events, browser storage, and safe API interaction.

## What you will practice

- Explain core concepts in plain language and connect them to a real UI feature.
- Choose APIs and patterns based on lifecycle, data flow, performance, and failure behavior.
- Recognize common interview traps and describe a practical debugging approach.

## Topic map

- [Language foundations](easy-interview-questions.md#language-foundations) — questions 1–10
- [DOM and events](easy-interview-questions.md#dom-and-events) — questions 11–20
- [Async work and APIs](easy-interview-questions.md#async-work-and-apis) — questions 21–30
- [Runtime behavior and gotchas](easy-interview-questions.md#runtime-behavior-and-gotchas) — questions 31–40


## Quick example

Cancelable search request:

```js
let activeController;

async function search(query) {
  activeController?.abort();
  activeController = new AbortController();
  const response = await fetch("/api/search?q=" + encodeURIComponent(query), {
    signal: activeController.signal
  });
  if (!response.ok) throw new Error("Search failed");
  return response.json();
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

- [MDN JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide)
- [MDN promises guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)
- [MDN JavaScript execution model](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Event_loop)
- [MDN Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
