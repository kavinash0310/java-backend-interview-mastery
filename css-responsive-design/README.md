# CSS, Layout, and Responsive Design

A practical guide to CSS syntax, the cascade, layout, responsive design, accessibility, and debugging styles in real interfaces.

## What you will practice

- Read and reason about the cascade, specificity, and inheritance.
- Choose Flexbox, Grid, and positioning based on the layout problem.
- Build responsive components that handle real content and varied viewports.
- Avoid common focus, motion, contrast, overflow, and stacking pitfalls.

## Topic map

- [CSS basics and cascade](easy-interview-questions.md#css-basics-and-cascade) — questions 1–10
- [Box model and layout](easy-interview-questions.md#box-model-and-layout) — questions 11–20
- [Responsive and accessible CSS](easy-interview-questions.md#responsive-and-accessible-css) — questions 21–30
- [Rendering and layout edge cases](easy-interview-questions.md#rendering-and-layout-edge-cases) — questions 31–40


## Quick example

Responsive card grid:

```css
.cards {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(min(100%, 16rem), 1fr));
  gap: 1rem;
}
```

## Question sets

- [Easy — 40 questions](easy-interview-questions.md)
- [Medium — 40 questions](medium-interview-questions.md)
- [Hard — 40 questions](hard-interview-questions.md)
- [Tricky — 40 questions](tricky-interview-questions.md)

## Study tip

For layout questions, explain which sizing constraint or formatting context is responsible, then show the smallest CSS change that fixes it. Include keyboard focus and zoom in responsive reviews.

## References

- [MDN CSS guides](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides)
- [MDN cascade and inheritance](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Styling_basics)
- [MDN Flexbox](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_flexible_box_layout)
- [MDN Grid](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout)
- [MDN media queries](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Media_queries)
