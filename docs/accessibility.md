# Accessibility

> Standards, patterns, and testing guide for building accessible interfaces.

---

## Table of Contents

1. [Standards](#standards)
2. [Principles](#principles)
3. [Keyboard Navigation](#keyboard-navigation)
4. [Focus Management](#focus-management)
5. [Screen Reader Support](#screen-reader-support)
6. [Color & Contrast](#color--contrast)
7. [Motion & Animation](#motion--animation)
8. [Forms](#forms)
9. [Images & Media](#images--media)
10. [Component Checklist](#component-checklist)
11. [Testing](#testing)
12. [Resources](#resources)

---

## Standards

This project targets **WCAG 2.1 Level AA** compliance for all user-facing interfaces.

| Criterion | Level | Requirement |
|-----------|-------|-------------|
| 1.1.1 Non-text Content | A | Alt text for images |
| 1.3.1 Info and Relationships | A | Semantic HTML structure |
| 1.4.3 Contrast (Minimum) | AA | 4.5:1 text, 3:1 large text |
| 1.4.4 Resize Text | AA | 200% without loss of content |
| 1.4.11 Non-text Contrast | AA | 3:1 for UI components |
| 2.1.1 Keyboard | A | All functionality via keyboard |
| 2.4.3 Focus Order | A | Logical focus sequence |
| 2.4.7 Focus Visible | AA | Visible focus indicator |
| 3.2.2 On Input | A | No unexpected context changes |
| 4.1.2 Name, Role, Value | A | Accessible names for controls |

---

## Principles

### Perceivable
- Provide text alternatives for non-text content
- Don't use color as the only means of conveying information
- Ensure sufficient contrast for text and UI elements

### Operable
- All functionality available via keyboard
- No keyboard traps
- Users can pause, stop, or hide moving content
- Provide sufficient time to read and interact

### Understandable
- Text is readable and understandable
- Pages behave predictably
- Help users avoid and correct errors

### Robust
- Use valid, semantic HTML
- Ensure compatibility with current and future assistive technologies

---

## Keyboard Navigation

### Focus Order
- Tab order must follow the visual reading order (top-to-bottom, left-to-right)
- Don't use `tabindex` values > 0 (creates maintenance nightmares)
- Use `tabindex="0"` only to make custom interactive elements focusable
- Use `tabindex="-1"` to programmatically focus elements without adding them to tab order

### Keyboard Shortcuts

| Component | Key | Action |
|-----------|-----|--------|
| Button | `Enter`, `Space` | Activate |
| Link | `Enter` | Follow |
| Checkbox | `Space` | Toggle |
| Radio group | `↑` `↓` or `←` `→` | Move between options |
| Select/Listbox | `↑` `↓` | Navigate; `Enter` to select |
| Dialog | `Escape` | Close |
| Tabs | `←` `→` | Switch tabs |
| Menu | `↑` `↓` | Navigate; `Enter` to select; `Escape` to close |
| Accordion | `Enter`, `Space` | Toggle panel |
| Slider | `←` `→` `↑` `↓` `Home` `End` | Adjust value |

---

## Focus Management

### Dialogs & Modals
```typescript
function openModal() {
  // 1. Save reference to trigger element
  lastFocusedElement = document.activeElement as HTMLElement;

  // 2. Open modal and move focus to first focusable element
  modal.removeAttribute("hidden");
  firstFocusableElement.focus();
}

function closeModal() {
  modal.setAttribute("hidden", "");
  // 3. Return focus to the element that opened the modal
  lastFocusedElement?.focus();
}
```

### Focus Trap
For modals, drawers, and dialogs:
```typescript
function trapFocus(container: HTMLElement) {
  const focusable = container.querySelectorAll<HTMLElement>(
    'a[href], button:not([disabled]), input:not([disabled]), ' +
    'select:not([disabled]), textarea:not([disabled]), ' +
    '[tabindex="0"]'
  );
  const first = focusable[0];
  const last = focusable[focusable.length - 1];

  container.addEventListener("keydown", (e) => {
    if (e.key !== "Tab") return;
    if (e.shiftKey && document.activeElement === first) {
      e.preventDefault();
      last.focus();
    } else if (!e.shiftKey && document.activeElement === last) {
      e.preventDefault();
      first.focus();
    }
  });
}
```

---

## Screen Reader Support

### Semantic HTML First
```html
<!-- ✅ -->
<button type="button" aria-expanded="false" aria-controls="menu">
  Options
</button>

<!-- ❌ -->
<div class="btn" onclick="toggle()">Options</div>
```

### Accessible Names
Every interactive element must have an accessible name:
```html
<!-- Icon-only button -->
<button aria-label="Close dialog">
  <svg aria-hidden="true">...</svg>
</button>

<!-- Input with label -->
<label for="email">Email address</label>
<input id="email" type="email" />

<!-- Labeled group -->
<fieldset>
  <legend>Notification preferences</legend>
  ...
</fieldset>
```

### Live Regions
```html
<!-- Status messages (non-urgent) -->
<div role="status" aria-live="polite" aria-atomic="true">
  Changes saved.
</div>

<!-- Error alerts (urgent) -->
<div role="alert" aria-live="assertive">
  Error: Please fix the highlighted fields.
</div>
```

### Loading States
```html
<button aria-busy="true" aria-label="Saving...">
  <svg aria-hidden="true" class="spinner">...</svg>
  Saving
</button>
```

---

## Color & Contrast

- Minimum contrast for body text: **4.5:1**
- Minimum contrast for large text (18px+ regular, 14px+ bold): **3:1**
- Minimum contrast for UI components (borders, icons): **3:1**
- Don't rely on color alone — pair with text, icons, or patterns

### Automated Checks
Use the browser DevTools Accessibility panel to check contrast ratios during development.

---

## Motion & Animation

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

- Provide controls to pause or stop any animation lasting > 5 seconds
- Avoid content that flashes more than 3 times per second (photosensitive epilepsy)

---

## Forms

```html
<!-- Associate label with input -->
<label for="name">Full name <span aria-hidden="true">*</span></label>
<input id="name" type="text" required aria-required="true"
       aria-describedby="name-error" autocomplete="name" />
<span id="name-error" role="alert" hidden>
  Please enter your full name.
</span>
```

- Every input must have a visible, associated label
- Error messages must be programmatically associated with the input (`aria-describedby`)
- Required fields must be marked visually and with `aria-required="true"`
- Group related fields with `<fieldset>` and `<legend>`
- Autocomplete attributes help users with cognitive disabilities

---

## Images & Media

```html
<!-- Informative image -->
<img src="chart.png" alt="Bar chart showing 40% increase in Q4 revenue" />

<!-- Decorative image -->
<img src="divider.svg" alt="" role="presentation" />

<!-- Complex image (long description) -->
<figure>
  <img src="diagram.png" alt="System architecture" aria-describedby="diagram-desc" />
  <figcaption id="diagram-desc">
    The system consists of three layers: ...
  </figcaption>
</figure>
```

- Videos must have captions and audio descriptions
- Audio must have transcripts
- Don't autoplay audio or video

---

## Component Checklist

For every component before shipping:

- [ ] Can be used with keyboard only
- [ ] Has visible focus indicator
- [ ] Has accessible name/label
- [ ] ARIA roles and properties are correct
- [ ] Color contrast meets 4.5:1 (text) and 3:1 (UI)
- [ ] Not relying on color alone for meaning
- [ ] Works with `prefers-reduced-motion`
- [ ] Screen reader announces state changes
- [ ] Error states are communicated accessibly
- [ ] Touch target is ≥ 44×44px

---

## Testing

### Automated
```bash
# Run axe accessibility audit
npx axe-core http://localhost:3000

# Lighthouse accessibility audit
npx lighthouse http://localhost:3000 --only-categories=accessibility
```

### Manual
1. **Keyboard test**: Tab through the entire page without a mouse
2. **Screen reader test**: VoiceOver (macOS), NVDA (Windows), TalkBack (Android)
3. **Zoom test**: Zoom browser to 200% — no content loss or horizontal scroll
4. **Contrast check**: Use DevTools or axe browser extension

---

## Resources

- [WCAG 2.1 Quick Reference](https://www.w3.org/WAI/WCAG21/quickref/)
- [ARIA Authoring Practices Guide (APG)](https://www.w3.org/WAI/ARIA/apg/)
- [axe DevTools](https://www.deque.com/axe/)
- [Inclusive Components](https://inclusive-components.design/)
- [A11y Project Checklist](https://www.a11yproject.com/checklist/)
