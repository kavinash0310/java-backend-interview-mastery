# CSS, Layout, and Responsive Design: Tricky Interview Questions and Answers

**40 questions.** Focus on the browser’s actual layout and cascade rules; use a small example when it makes the trade-off easier to explain.

## CSS basics and cascade

### 1. Does a more specific selector always win?

**Answer:** No. Origin, importance, cascade layer, and other cascade stages are considered before specificity.

### 2. Does !important always beat every other declaration?

**Answer:** No. Importance is evaluated within origin and layer ordering, and transitions can outrank important declarations while active.

### 3. Does a child inherit its parent’s width?

**Answer:** Usually not. width is not inherited by default, though auto sizing often makes the child appear to fill its container.

### 4. Does margin:auto vertically center a block?

**Answer:** Not in every ordinary block-flow situation; use a layout context such as Flexbox or Grid for reliable alignment.

### 5. Does z-index work on every element?

**Answer:** Only when the element participates in stacking behavior where z-index applies; even then ancestor stacking contexts constrain it.

### 6. Can a child’s z-index escape its parent stacking context?

**Answer:** No. The parent context is painted as a unit relative to sibling contexts.

### 7. Is position:relative removed from normal flow?

**Answer:** No. It remains in flow; offsets move its visual position without changing the original layout slot.

### 8. Does position:sticky always stick to the viewport?

**Answer:** No. It sticks relative to its nearest scroll container and containing block, subject to its inset and available space.

### 9. Does display:none merely make content invisible?

**Answer:** It removes the element’s box from layout and generally makes it unavailable in the accessibility tree.

### 10. Does visibility:hidden remove layout space?

**Answer:** No. Its box remains in layout, but it is not visibly painted and is typically not exposed for interaction.

## Box model and layout

### 11. Does opacity:0 make a control disabled?

**Answer:** No. It may still be focusable and clickable; visibility and interaction state are separate.

### 12. Does pointer-events:none stop keyboard activation?

**Answer:** No. It affects pointer targeting, not necessarily keyboard focus or activation.

### 13. Does flex:1 guarantee equal final widths?

**Answer:** Not always. Basis, min/max sizes, content constraints, margins, and available space affect the final sizing.

### 14. Does align-items:center center a flex row horizontally?

**Answer:** Only if the cross axis is horizontal; for flex-direction:row the cross axis is vertical.

### 15. Does justify-content:center always center the whole group?

**Answer:** It centers free space in the container’s main axis, but overflow and auto margins can change the result.

### 16. Does 1fr mean exactly one equal fraction of the container width?

**Answer:** It is a flexible track fraction after intrinsic sizing and constraints; content minimums can affect track distribution.

### 17. Can an element be wider than its grid track?

**Answer:** Yes. Min-content constraints, fixed children, or overflow can exceed the track unless sizing and wrapping are controlled.

### 18. Does auto-fit always create more columns than auto-fill?

**Answer:** No. They use the same repeat-fit calculation; auto-fit collapses empty tracks, while auto-fill keeps them.

### 19. Are em units always relative to the parent font size?

**Answer:** For font-size, em is relative to the inherited font size; for many other properties it is based on the element’s computed font size.

### 20. Does 100vh always equal the visible mobile viewport?

**Answer:** Not reliably with dynamic browser UI; modern viewport units distinguish small, large, and dynamic viewport sizes.

## Responsive and accessible CSS

### 21. Does a media query detect the device model?

**Answer:** No. It tests exposed environment features such as viewport, pointer, hover, or motion preference, not a specific model.

### 22. Does a breakpoint need to match popular phone widths?

**Answer:** No. Choose it where the content or layout needs to change, then validate a range of widths.

### 23. Does overflow:hidden only clip paint?

**Answer:** It also establishes a scroll container in relevant cases and can clip focus rings or content that users need.

### 24. Does a transform affect only appearance?

**Answer:** No. It can create a stacking context and containing block, influencing fixed descendants and z-index behavior.

### 25. Does setting transform:translateZ(0) always speed up a page?

**Answer:** No. It can increase compositing memory and create new stacking behavior; profile before using it as a hack.

### 26. Does will-change improve performance whenever it is added?

**Answer:** No. It is a hint with resource costs; browsers may reserve resources or create contexts before the change occurs.

### 27. Can CSS variables be used as media-query breakpoints?

**Answer:** Custom properties cannot generally substitute for media-query condition values; use build-time variables or explicit query values.

### 28. Does var(--x, fallback) use fallback when --x is an empty string?

**Answer:** No. The fallback applies when the custom property is invalid or missing, not merely empty.

### 29. Do custom properties inherit automatically?

**Answer:** Yes, unless registered with @property using inherits:false or otherwise handled through inheritance rules.

### 30. Does :where() add selector specificity?

**Answer:** No. :where() and its arguments contribute zero specificity.

## Rendering and layout edge cases

### 31. Does :is() always have zero specificity?

**Answer:** No. It takes the specificity of its most specific argument.

### 32. Can :has() match an ancestor based on a descendant?

**Answer:** Yes, it is a relational pseudo-class; compatibility and performance still matter for the target browser set.

### 33. Does a CSS animation obey reduced-motion automatically?

**Answer:** No. The author must respect prefers-reduced-motion and provide a suitable alternative.

### 34. Does removing outline make focus invisible only to keyboard users?

**Answer:** It removes a major visual cue for keyboard focus; do not remove it without a clear replacement.

### 35. Can a pseudo-element replace meaningful text content?

**Answer:** Not reliably. Generated content may be unavailable or poorly announced; keep essential content in the document.

### 36. Does a larger font always increase a rem-based margin?

**Answer:** Only if the root font size changes; rem follows the root size, not every local font-size change.

### 37. Does width:100% include padding by default?

**Answer:** With content-box it sizes the content box, so padding and border add to the outer width; border-box changes that.

### 38. Does min-width:0 make text wrap by itself?

**Answer:** No. It permits shrinking; wrapping behavior may still require overflow-wrap or another text rule.

### 39. Can CSS reorder visual content without changing keyboard order?

**Answer:** Yes, properties like order can do this; keep visual sequence aligned with reading and focus order.

### 40. Does sticky positioning work if no top or inset is set?

**Answer:** Usually it has no threshold to stick against; provide an inset such as top:0.

## References

- [MDN CSS guides](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides)
- [MDN cascade and inheritance](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Styling_basics)
- [MDN Flexbox](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_flexible_box_layout)
- [MDN Grid](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout)
- [MDN media queries](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Media_queries)
