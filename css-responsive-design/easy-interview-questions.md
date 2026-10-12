# CSS, Layout, and Responsive Design: Easy Interview Questions and Answers

**40 questions.** Focus on the browser’s actual layout and cascade rules; use a small example when it makes the trade-off easier to explain.

## CSS basics and cascade

### 1. What does CSS do?

**Answer:** CSS controls how structured content is presented, including layout, colors, typography, spacing, and responsive behavior.

### 2. What does CSS stand for?

**Answer:** Cascading Style Sheets; “cascading” describes how competing declarations are resolved.

### 3. How can CSS be added to a page?

**Answer:** Use an external stylesheet with link, an internal style element, or an inline style attribute; external styles are easiest to reuse.

### 4. What is a CSS rule?

**Answer:** A selector chooses elements, and declarations inside braces assign property values, for example p { color: navy; }.

### 5. What is a selector?

**Answer:** A selector identifies which elements a CSS rule should match, such as a tag, class, id, or attribute selector.

### 6. What is the difference between a class selector and an id selector?

**Answer:** .class matches reusable class values; #id matches an element with that id. Prefer classes for styling.

### 7. What does the color property set?

**Answer:** It sets the foreground text color, not the background.

### 8. What does background-color set?

**Answer:** It sets an element’s background color behind its content and padding.

### 9. What is the CSS box model?

**Answer:** An element’s layout consists of content, padding, border, and margin.

### 10. What does padding control?

**Answer:** Padding is the space between an element’s content and its border.

## Box model and layout

### 11. What does margin control?

**Answer:** Margin is space outside an element’s border, separating it from neighboring boxes.

### 12. What does border-box sizing mean?

**Answer:** With box-sizing:border-box, declared width includes content, padding, and border, which simplifies sizing.

### 13. How do you make text bold in CSS?

**Answer:** Use font-weight, for example font-weight: 700; semantic importance belongs in HTML when relevant.

### 14. How do you set the font size?

**Answer:** Use font-size with a unit such as rem, em, px, or a responsive value such as clamp().

### 15. What is a pseudo-class?

**Answer:** It selects an element in a state or position, such as :hover, :focus-visible, or :first-child.

### 16. What is a pseudo-element?

**Answer:** It targets a generated part of an element, such as ::before or ::marker.

### 17. What does display:block do?

**Answer:** It makes an element generate a block box that normally starts on a new line and can take available width.

### 18. What does display:inline do?

**Answer:** It keeps content in the text flow; width and height generally do not size inline non-replaced elements.

### 19. What does display:none do?

**Answer:** It removes the element from layout and typically from the accessibility tree until it is shown again.

### 20. What is Flexbox useful for?

**Answer:** Flexbox lays out items along one main axis, making it useful for rows, columns, alignment, and distributing free space.

## Responsive and accessible CSS

### 21. What is CSS Grid useful for?

**Answer:** Grid lays out content across rows and columns, making it useful for two-dimensional page or component layouts.

### 22. How do you center text?

**Answer:** Use text-align:center on the appropriate block container; this centers inline content, not the container itself.

### 23. How can you center a box horizontally with a known width?

**Answer:** Set a width or max-width and use margin-inline:auto.

### 24. What does position:relative do?

**Answer:** It keeps the element in normal flow but allows offsets and creates a positioning context for positioned descendants.

### 25. What does position:absolute do?

**Answer:** It removes the element from normal flow and positions it against its nearest positioned ancestor or initial containing block.

### 26. What does z-index control?

**Answer:** It controls stacking order within the relevant stacking context; larger values do not escape an ancestor context.

### 27. What is a media query?

**Answer:** It applies CSS conditionally based on viewport or device features, such as width or reduced-motion preference.

### 28. What is a responsive layout?

**Answer:** A responsive layout adapts to different available sizes and input environments instead of assuming one fixed screen.

### 29. What does max-width do?

**Answer:** It caps the used width so content can shrink on small screens without becoming too wide on large ones.

### 30. Why use rem for many spacing values?

**Answer:** rem is based on the root font size, making a spacing scale easier to adjust consistently.

## Rendering and layout edge cases

### 31. What is a CSS custom property?

**Answer:** A custom property such as --brand-color stores a reusable value and is read with var(--brand-color).

### 32. How do you use a custom property?

**Answer:** Declare --space: 1rem; on a selector and reference it with padding: var(--space).

### 33. What does overflow:hidden do?

**Answer:** It clips overflowing content; that can also hide focus outlines or needed content, so use it deliberately.

### 34. What is opacity:0?

**Answer:** It makes an element transparent but does not by itself remove its layout, pointer behavior, or accessibility exposure.

### 35. What is the purpose of :focus-visible?

**Answer:** It styles focus when the browser determines a visible indicator is appropriate, especially for keyboard navigation.

### 36. What is a CSS reset or normalization stylesheet?

**Answer:** It reduces or aligns browser default styles so a project starts from more predictable base rules.

### 37. What is inheritance in CSS?

**Answer:** Some property values flow from a parent to descendants unless overridden; color commonly inherits, margin generally does not.

### 38. What is the cascade?

**Answer:** It is the process browsers use to choose a winning declaration based on origin, importance, layers, specificity, and order.

### 39. How can you load a stylesheet?

**Answer:** Use <link rel="stylesheet" href="styles.css"> in the document head.

### 40. What is a breakpoint?

**Answer:** A condition where layout rules change because the current design no longer fits the available space comfortably.

## References

- [MDN CSS guides](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides)
- [MDN cascade and inheritance](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Styling_basics)
- [MDN Flexbox](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_flexible_box_layout)
- [MDN Grid](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout)
- [MDN media queries](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Media_queries)
