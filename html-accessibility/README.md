# HTML and Web Accessibility

This module covers semantic HTML, accessible forms and controls, document structure, metadata, media, keyboard behavior, and practical accessibility review.

## What you will practice

- Choose elements based on their meaning and built-in browser behavior.
- Build forms with labels, grouping, useful validation, and clear errors.
- Support keyboard and assistive-technology navigation.
- Review common accessibility and markup pitfalls in real interfaces.

## Topic map

- [Document basics](easy-interview-questions.md#document-basics) — questions 1–10
- [Structure and forms](easy-interview-questions.md#structure-and-forms) — questions 11–20
- [Semantics and keyboard access](easy-interview-questions.md#semantics-and-keyboard-access) — questions 21–30
- [Accessible patterns and edge cases](easy-interview-questions.md#accessible-patterns-and-edge-cases) — questions 31–40


## Quick example

Accessible form field:

```html
<form>
  <label for="email">Email</label>
  <input id="email" name="email" type="email" required>
  <button type="submit">Continue</button>
</form>
```

## Question sets

- [Easy — 40 questions](easy-interview-questions.md)
- [Medium — 40 questions](medium-interview-questions.md)
- [Hard — 40 questions](hard-interview-questions.md)
- [Tricky — 40 questions](tricky-interview-questions.md)

## Study tip

Answer aloud, then give a concrete example from a page or form you have built. For interactive patterns, explain which native element you would use before reaching for ARIA.

## References

- [MDN HTML elements reference](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements)
- [MDN forms guide](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Forms)
- [MDN semantic HTML](https://developer.mozilla.org/en-US/curriculum/core/semantic-html/)
- [WHATWG HTML Living Standard](https://html.spec.whatwg.org/multipage/)
