# Frontend Developer Interview Guide

> **For junior developers** — HTML/CSS, JavaScript, Responsive Design & React best practices  
> Senior Technical Interviewer Edition · All questions, rubrics, and project tasks in one file

---

## Table of Contents

1. [Interview Structure](#interview-structure)
2. [Assessment Criteria](#assessment-criteria)
3. [Semantic HTML Questions](#semantic-html-questions)
4. [CSS & Responsive Design Questions](#css--responsive-design-questions)
5. [JavaScript Questions](#javascript-questions)
6. [React Best Practices Questions](#react-best-practices-questions)
7. [Project-Based Tasks](#project-based-tasks)
8. [Evaluation Rubrics](#evaluation-rubrics)
9. [Score Calculator Reference](#score-calculator-reference)
10. [Interviewer Tips](#interviewer-tips)

---

## Interview Structure

| Phase | Duration | Focus |
|-------|----------|-------|
| 1. Warm-up | 10 min | Background, motivation, recent projects |
| 2. Concept round | 25 min | HTML semantics, CSS layout, JS fundamentals |
| 3. Live coding | 30 min | Build a responsive card component from scratch |
| 4. React round | 15 min | State, effects, hooks, component design |
| 5. Wrap-up | 10 min | Candidate questions, feedback preview |

**Total session:** ~90 minutes  
**Pass threshold:** ≥ 65% weighted score  
**Strong hire threshold:** ≥ 80% with no critical gaps

---

## Assessment Criteria

| Category | Weight | Core Skills Tested |
|----------|--------|--------------------|
| Semantic HTML | 20% | Landmark roles, ARIA, accessibility, forms |
| CSS & Responsive | 20% | Grid, Flexbox, mobile-first, custom properties |
| JavaScript | 25% | Closures, async, DOM, event loop |
| React | 25% | Hooks, state, reconciliation, patterns |
| Communication | 10% | Reasoning, trade-offs, feedback reception |

> **Critical gap rule:** A score of 1 in any single category is an automatic no-hire regardless of the overall average. Two or more scores of 2 indicate foundational issues that mentoring alone won't resolve quickly.

---

## Semantic HTML Questions

### Basic

---

**Q1 — Why use `<button>` instead of `<div onclick>` for clickable actions?**

**What you're looking for:** Keyboard accessibility (Tab/Enter/Space), ARIA role assignment, focus management, built-in browser affordances.

**Red flags:** "They're the same thing", no mention of accessibility, can't describe keyboard behavior.

**Strong answer:**  
Buttons are focusable by default, fire click events on Enter and Space, expose `role="button"` to screen readers, inherit form submission behavior, and support the `disabled` attribute natively. A `<div>` requires `tabindex="0"`, `role="button"`, and manual key event handlers — extra implementation work to replicate what `<button>` provides for free. The semantic element also gives assistive technology the correct affordance cue without any additional markup.

---

**Q2 — What is the purpose of `<main>`, `<nav>`, `<header>`, and `<footer>`? Can you have more than one of each?**

**What you're looking for:** Landmark roles, screen reader navigation, one `<main>` per page rule, multiple contextual uses of other landmarks.

**Strong answer:**  
These are landmark elements that expose navigation regions to assistive technology. `<main>` wraps the primary content — there should be exactly one per page. `<header>` and `<footer>` can appear inside `<article>` or `<section>` elements to describe the header/footer of that content block, not just the page. Multiple `<nav>` elements are valid and common (primary nav, breadcrumb, pagination), but each should carry a distinct `aria-label` so screen reader users can distinguish them.

---

### Intermediate

---

**Q3 — When would you choose `<article>` vs `<section>`? Give an example of each.**

**Key distinction:**  
`<article>` is self-contained content that could be syndicated or extracted independently — a blog post, a news item, a product card, a comment. `<section>` groups thematically related content within a page but is not meaningful outside its surrounding context.

**Example:**  
A blog listing page uses `<article>` for each post. Inside a long article, `<section>` can divide the introduction, body, and conclusion if those sections are substantial enough to warrant navigation.

---

**Q4 — Explain the difference between `alt=""` (empty string) and omitting the `alt` attribute entirely.**

**Key distinction:**  
`alt=""` explicitly marks the image as decorative — screen readers skip it entirely. Omitting `alt` leaves the image with no accessible name, which causes screen readers to fall back to announcing the filename (e.g., "hero-banner-2024-v3-final.png") — unhelpful and disruptive to users.

**Rule of thumb:** Decorative images (background flourishes, icons already labelled by nearby text) get `alt=""`. Informational images get a meaningful description that conveys the content or function the image provides.

---

**Q5 — How do you make a form accessible? Walk through a complete field — label, input, helper text, and error state.**

**Expected elements:** `<label for="id">` linked to input via matching `id`. Helper text referenced by `aria-describedby`. Error message injected into a live region or referenced by `aria-describedby` alongside `aria-invalid="true"`. Focus should move to the first errored field on submit failure.

```html
<label for="email">Email address</label>
<input
  id="email"
  type="email"
  aria-describedby="email-hint email-err"
  aria-invalid="true"
/>
<p id="email-hint">We'll never share your email.</p>
<p id="email-err" role="alert">Enter a valid email address.</p>
```

**Note:** Using `role="alert"` on the error paragraph causes screen readers to announce it immediately when it appears. The `aria-describedby` association means the full context is also read when the input receives focus.

---

### Advanced

---

**Q6 — Describe the ARIA authoring practices for a custom dropdown (combobox pattern). What keyboard interactions are required?**

**Required keyboard interactions:**
- Arrow keys to move through options
- Enter or Space to select the focused option
- Escape to close the listbox
- Home / End to jump to first / last option
- Type-ahead filtering (optional but strongly recommended)

**Required ARIA attributes:**
- Trigger button: `aria-expanded`, `aria-haspopup="listbox"`, `aria-controls`
- Listbox: `role="listbox"`
- Options: `role="option"`, `aria-selected`
- Active option tracked via `aria-activedescendant` on the trigger (focus stays on the trigger, not the list items)

**Bonus:** Distinguish between a select-only combobox (no text input) and an editable combobox (with filtering), as the patterns differ meaningfully.

---

**Q7 — What is the document outline algorithm and why does heading hierarchy matter for screen readers?**

**Core point:** Screen reader users navigate by headings using shortcut keys (H in NVDA/JAWS). Skipping heading levels — e.g., jumping from `<h2>` to `<h4>` — breaks the logical outline and makes navigation unpredictable.

**Rules:**
- One `<h1>` per page, representing the page's primary topic
- Sections nest headings incrementally (h1 → h2 → h3)
- Never choose a heading level for visual size — that's a CSS concern

**Common mistake:** Developers pick heading levels based on how large the text should appear rather than where the heading sits in the document hierarchy.

---

## CSS & Responsive Design Questions

### Basic

---

**Q8 — Explain the CSS box model. What does `box-sizing: border-box` change?**

**Box model layers (inside out):** content → padding → border → margin

**Default `content-box`:** `width` sets the content area only. Padding and border are added on top of that, making the total rendered size larger than the declared width.

**`border-box`:** `width` includes padding and border. The element stays at exactly the declared width regardless of padding values. This makes layout math far more predictable, which is why modern CSS resets apply `* { box-sizing: border-box }` universally.

---

**Q9 — What is specificity? How would you resolve a conflict between a class and an ID selector?**

**Specificity hierarchy (low to high):**

| Selector type | Specificity value |
|---------------|------------------|
| Element (`p`, `div`) | (0, 0, 1) |
| Class, pseudo-class, attribute | (0, 1, 0) |
| ID (`#header`) | (1, 0, 0) |
| Inline style | (1, 0, 0, 0) |
| `!important` | Overrides all |

**Resolution:** Since IDs beat classes, prefer class selectors and keep specificity flat across a codebase. Methodologies like BEM or scoped CSS Modules prevent specificity conflicts without needing `!important`.

---

### Intermediate

---

**Q10 — Build a 3-column responsive grid that collapses to 1 column below 600px using only CSS Grid — no media queries.**

```css
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
  gap: 1rem;
}
```

**Key insight:** `auto-fit` + `minmax()` is the intrinsic sizing approach. The grid collapses naturally when the container can no longer fit a 180px column — no breakpoints needed.

**Bonus distinction:** `auto-fit` collapses empty tracks so filled columns expand to fill space. `auto-fill` keeps empty tracks, leaving gaps. Usually `auto-fit` is what you want.

---

**Q11 — What is a mobile-first approach and why is it preferred over desktop-first?**

**Mobile-first:** Base styles target small screens. `min-width` breakpoints layer progressive enhancement as the viewport grows.

**Desktop-first:** Base styles target large screens. `max-width` queries strip features for smaller viewports.

**Why mobile-first is preferred:**
- Progressive enhancement — start minimal, add complexity as space permits
- Avoids overriding heavy desktop styles on low-powered mobile devices
- Better performance on slower connections (smaller default CSS payload)
- Forces content prioritization decisions early in the design process

---

**Q12 — Explain the difference between Flexbox and CSS Grid. When would you use one over the other?**

| | Flexbox | CSS Grid |
|-|---------|----------|
| Dimensions | One-dimensional (row OR column) | Two-dimensional (rows AND columns) |
| Driven by | Content size | Layout definition |
| Best for | Navbars, button groups, centering, distributing items along one axis | Page layouts, card grids, dashboards, any layout needing both axes |

**Rule of thumb:** If the layout is driven by the content, use Flexbox. If you're defining the layout container first and placing content into it, use Grid. They complement each other — it's common to use Grid for page structure and Flexbox for component internals.

---

### Advanced

---

**Q13 — What are CSS custom properties? How do they differ from Sass variables, and how would you use them for theming?**

**Key difference:** CSS custom properties are live and cascade — they can be modified at runtime via JavaScript, scoped to any element, and overridden with media queries. Sass variables are compiled away before the browser sees them — no runtime access, no cascade.

```css
/* Token definition */
:root {
  --color-primary: #0070f3;
  --color-surface: #ffffff;
}

/* Dark mode override */
[data-theme="dark"] {
  --color-primary: #3b9eff;
  --color-surface: #0f0f0f;
}

/* Consumption — same code, both themes */
.button {
  background: var(--color-primary);
  color: var(--color-surface);
}
```

**Theming pattern:** Define all design tokens on `:root`, override on a `[data-theme]` attribute, consume via `var()` everywhere. No JavaScript needed to switch themes — just toggle the attribute.

---

**Q14 — How would you approach a fluid typography system that scales smoothly between mobile and desktop without fixed breakpoints?**

```css
/* Fluid heading: 1.5rem at small → 3rem at large */
h1 {
  font-size: clamp(1.5rem, 2.5vw + 1rem, 3rem);
}

/* clamp(minimum, preferred-calculation, maximum) */
```

**How it works:** `clamp()` takes three values. The preferred value is a linear interpolation using `vw` units, so font size grows proportionally with the viewport. The min and max clamp it at sensible bounds.

**Tooling:** Tools like [utopia.fyi](https://utopia.fyi) calculate the correct coefficients for a full fluid type scale across a defined viewport range.

---

## JavaScript Questions

### Basic

---

**Q15 — What is the difference between `==` and `===`? Give an example where they produce different results.**

`==` performs type coercion before comparison. `===` checks value and type strictly — no coercion.

```js
"5" == 5          // true  (string coerced to number)
"5" === 5         // false (different types)
null == undefined // true  (special coercion rule)
null === undefined// false (different types)
0 == false        // true  (coercion)
0 === false       // false
```

**Best practice:** Always use `===`. Type coercion in `==` produces surprising results and is a common source of bugs.

---

**Q16 — Explain `var`, `let`, and `const`. What is hoisting?**

| | `var` | `let` | `const` |
|-|-------|-------|---------|
| Scope | Function | Block | Block |
| Hoisted? | Yes, as `undefined` | Yes, but temporal dead zone | Yes, but temporal dead zone |
| Reassignable? | Yes | Yes | No (binding only) |

**Hoisting:** Variable and function declarations are moved to the top of their scope at compile time. Only the declaration is hoisted, not the initialisation. Accessing a `let`/`const` before its declaration throws a `ReferenceError` (temporal dead zone).

**Note on `const`:** The binding can't be reassigned, but object properties and array contents can still mutate — `const` is not deep immutability.

---

### Intermediate

---

**Q17 — What is a closure? Provide a practical use case — not the classic counter example.**

**Definition:** A function that retains access to variables from its enclosing scope even after that scope has finished executing.

**Practical examples:**

```js
// 1. Pre-bound API base URL
function createFetcher(baseUrl) {
  return (path) => fetch(baseUrl + path);
}
const api = createFetcher('https://api.example.com');
api('/users'); // closes over baseUrl

// 2. Debounce — closes over the timer reference
function debounce(fn, delay) {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}

// 3. Module pattern — private state
function createCart() {
  const items = [];
  return {
    add: (item) => items.push(item),
    total: () => items.reduce((sum, i) => sum + i.price, 0),
  };
}
```

---

**Q18 — Explain event delegation. Why is it preferable to attaching listeners to every list item?**

**Pattern:** Attach one listener to a parent element. When a child is interacted with, the event bubbles up. Check `event.target` to determine which child triggered it.

```js
// One listener handles all current and future list items
list.addEventListener('click', (e) => {
  const item = e.target.closest('li[data-id]');
  if (item) handleItem(item.dataset.id);
});
```

**Benefits:**
- Lower memory footprint — N listeners becomes 1
- Works for dynamically added children without re-attaching
- Simpler cleanup — remove one listener, not many

---

**Q19 — Explain the difference between `Promise`, `async/await`, and callbacks. When would you use each?**

**Callbacks:** Pass a function to be called when async work finishes. Simple for a single operation, but leads to "callback hell" for chained operations and makes error propagation fragile.

**Promises:** Represent a future value. Chainable via `.then()` / `.catch()`. Avoids nesting. Essential for combinators like `Promise.all()`, `Promise.allSettled()`, `Promise.race()`.

**async/await:** Syntactic sugar over Promises. Reads synchronously, much easier to reason about and debug. Wrap in `try/catch` for error handling.

```js
// Preferred for sequential operations
async function loadDashboard(userId) {
  try {
    const user = await fetchUser(userId);
    const posts = await fetchPosts(user.id);
    return { user, posts };
  } catch (err) {
    handleError(err);
  }
}

// Promise.all for parallel operations
const [user, settings] = await Promise.all([
  fetchUser(id),
  fetchSettings(id),
]);
```

---

### Advanced

---

**Q20 — What is the event loop? Explain the difference between the microtask queue and the macrotask queue.**

**Event loop:** JavaScript is single-threaded. The event loop monitors the call stack; when it's empty, it processes queued tasks.

**Macrotask queue:** `setTimeout`, `setInterval`, I/O callbacks. One task is processed per event loop tick.

**Microtask queue:** Promise `.then()` / `.catch()`, `queueMicrotask()`, `MutationObserver` callbacks. The entire microtask queue is drained before the next macrotask begins.

```js
setTimeout(() => console.log('macro'), 0);
Promise.resolve().then(() => console.log('micro'));
console.log('sync');

// Output order:
// sync
// micro
// macro
```

**Why it matters:** A long chain of resolved Promises can starve the rendering pipeline or delay macrotasks — important to understand when debugging perceived performance issues.

---

## React Best Practices Questions

### Basic

---

**Q21 — What is the difference between props and state? When should each be used?**

**Props:** Read-only data passed from parent to child. A component cannot modify its own props. Used for configuration, data, and callbacks flowing downward.

**State:** Internal, mutable data managed by the component itself. Changes via `useState`/`useReducer` trigger a re-render.

**Decision rule:** If a value originates outside the component (passed in), it's a prop. If the component generates, owns, and changes it over time, it's state.

---

**Q22 — Why does React need a `key` prop in lists? What happens if you use the array index as a key?**

**Purpose:** Keys give React stable identities for list items across renders, enabling efficient reconciliation — adding, removing, and reordering without re-mounting unaffected items.

**Index as key problem:** When items are reordered or inserted in the middle, indices shift. React maps the item at the new index 0 to the DOM node that previously belonged to the old index 0. This causes controlled input state (e.g., typed values), animation state, and focus to bind to the wrong items.

**Best practice:** Use a stable, unique ID from the data (`item.id`, `item.slug`). Only use index if the list is static, never reordered, and items have no internal state.

---

### Intermediate

---

**Q23 — Explain `useEffect` dependency arrays. What happens with `[]`, no array, and a populated array?**

```js
// Runs after every single render
useEffect(() => { syncSomething(); });

// Runs once after mount (componentDidMount equivalent)
useEffect(() => {
  const sub = subscribe(id);
  return () => sub.unsubscribe(); // cleanup on unmount
}, []);

// Runs after mount AND whenever `id` or `query` changes
useEffect(() => {
  fetchData(id, query);
}, [id, query]);
```

**Common mistake:** Missing dependencies creates stale closures — the effect closes over values that no longer reflect the current render. The ESLint `exhaustive-deps` rule from `eslint-plugin-react-hooks` catches this automatically.

**Cleanup functions:** The function returned from `useEffect` runs before the next effect execution and on unmount. Always clean up subscriptions, timers, and event listeners to prevent memory leaks.

---

**Q24 — What is prop drilling? What are two ways to solve it?**

**Problem:** Passing props through intermediate components that don't use them — purely to get data to a deeply nested descendant. It creates tight coupling and makes refactoring painful.

**Solution 1 — React Context API:**

```jsx
const ThemeContext = createContext('light');

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <DeepTree />
    </ThemeContext.Provider>
  );
}

function DeepChild() {
  const theme = useContext(ThemeContext);
  return <div className={theme}>...</div>;
}
```

Best for global-ish data: theme, auth, locale, feature flags.

**Solution 2 — Component composition:**  
Pass rendered components as children or props so the component that owns the data renders the consumer directly — eliminating the intermediary entirely. Often underused but avoids adding state management overhead.

---

**Q25 — When would you use `useMemo` and `useCallback`? What is the cost of overusing them?**

**`useMemo`:** Memoizes a computed value — recalculates only when listed dependencies change.

```js
const filtered = useMemo(
  () => largeList.filter(item => item.active),
  [largeList]
);
```

Use for: expensive computations in the render path (filtering/sorting large datasets, complex derived data).

**`useCallback`:** Memoizes a function reference.

```js
const handleClick = useCallback(() => {
  doSomething(id);
}, [id]);
```

Use for: callbacks passed to `React.memo`-wrapped children, to prevent unnecessary re-renders caused by new function references each render.

**Cost of overuse:** Both hooks add memory overhead and comparison logic. They can actually make performance worse if the memoized value is cheap to compute. Measure with React DevTools Profiler before optimizing — premature memoization is a real anti-pattern.

---

### Advanced

---

**Q26 — What is a custom hook? Write one that abstracts a debounced search input.**

**Definition:** A function whose name starts with `use` that internally calls other hooks. Enables reusing stateful logic across components without changing the component tree hierarchy.

```js
// useDebounce.js
import { useState, useEffect } from 'react';

function useDebounce(value, delay = 300) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    // Cleanup clears the timer if value changes before delay elapses
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}

// Usage in a search component
function SearchBar() {
  const [input, setInput] = useState('');
  const debouncedQuery = useDebounce(input, 400);

  useEffect(() => {
    if (debouncedQuery) fetchResults(debouncedQuery);
  }, [debouncedQuery]);

  return (
    <input
      value={input}
      onChange={(e) => setInput(e.target.value)}
      placeholder="Search..."
    />
  );
}
```

---

**Q27 — Describe the React reconciliation algorithm. What is the virtual DOM and what are its trade-offs?**

**Virtual DOM:** An in-memory JavaScript representation of the real DOM tree. React maintains two trees — the current tree and the work-in-progress tree — and computes a minimal diff between them before applying changes to the real DOM.

**Reconciliation heuristics:**
1. Elements of different types → tear down the entire subtree and rebuild
2. Same element type → update only the changed attributes/props
3. `key` props give list items stable identities, enabling efficient reorder detection

**Trade-offs:**

| Benefit | Cost |
|---------|------|
| Predictable rendering model | Extra diffing overhead per render |
| Batched DOM updates | Memory for maintaining two trees |
| Great DX — describe UI declaratively | Abstraction can obscure what's actually hitting the DOM |

**React 18 concurrent features:** The reconciler can now pause, interrupt, and resume work. `startTransition` marks state updates as non-urgent, keeping the UI responsive during heavy re-renders.

---

## Project-Based Tasks

### Task 1 — Responsive event card grid

**Duration:** 45 minutes  
**Topics:** HTML · CSS · Responsive · Accessibility

**Scenario:**  
You're building a hackathon event listing page. Create a responsive grid of event cards that collapses gracefully from 3 columns → 2 → 1 as the viewport narrows. No JavaScript required.

**Requirements:**
- Cards include: event title, date/time, location, category badge, and a "Register" CTA
- Grid uses CSS Grid with intrinsic breakpoints — no media queries
- Semantic HTML: correct landmark roles, heading hierarchy, `<a>` vs `<button>` used appropriately
- Accessible: each card's CTA has a distinguishable `aria-label` or unique visible text
- A "Featured" event card spans full width or is visually elevated above the others

**Evaluation checklist:**
- [ ] Grid actually responds intrinsically without a single `@media` query
- [ ] All interactive elements are keyboard accessible and have visible focus styles
- [ ] Heading hierarchy is logical (`<h1>` for page title, `<h2>` per card)
- [ ] CTA links have accessible names that distinguish them (not 8 identical "Register" links)
- [ ] Featured card is meaningfully differentiated, not just a CSS class with no semantic signal

---

### Task 2 — Hackathon registration form

**Duration:** 40 minutes  
**Topics:** HTML · JavaScript · Accessibility · Forms

**Scenario:**  
Build a multi-field registration form for a hackathon. Validation runs on submit; errors are surfaced inline and announced to screen readers without a page reload.

**Requirements:**
- Fields: full name, email, team name (optional), track (select), dietary requirements (checkbox group)
- Inline validation with `aria-describedby` linking error messages to their inputs
- Error summary panel at the top on submit failure, with anchor links scrolling to each errored field
- Focus moves programmatically to the first error after a failed submit attempt
- All fields have visible, programmatic `<label>` elements — placeholder-only labels are not acceptable

**Bonus points:**
- Persist partial form data to `localStorage` so the user doesn't lose progress on refresh
- Live character count on a "project idea" `<textarea>` using an ARIA live region

**Evaluation checklist:**
- [ ] No input relies solely on placeholder as its label
- [ ] Error messages are programmatically associated with their inputs
- [ ] Focus management is implemented on submit failure
- [ ] The form is fully operable with keyboard only
- [ ] Select and checkbox group are correctly labelled with `<fieldset>` + `<legend>`

---

### Task 3 — React team management dashboard

**Duration:** 60 minutes  
**Topics:** React · State management · Hooks · Async

**Scenario:**  
Build a hackathon team dashboard in React. Teams can be added, filtered by track, and marked as submitted. Simulate a real API with a mock async fetch function.

**Requirements:**
- Fetch initial team list from a mock async function with an 800ms simulated delay
- Display distinct loading, error, and empty states
- Filter teams by track using a controlled `<select>` input
- Mark a team as "submitted" — update state optimistically (don't wait for a fake API response)
- Add a new team via a controlled form — validate that the team name is non-empty before adding
- No external state library — use `useState` and `useReducer` as appropriate

**Mock fetch to provide candidates:**

```js
const TEAMS = [
  { id: 1, name: 'Binary Stars', track: 'AI/ML', submitted: false },
  { id: 2, name: 'Stack Overflow', track: 'Web', submitted: false },
  { id: 3, name: 'Kernel Panic', track: 'Systems', submitted: true },
];

async function fetchTeams() {
  await new Promise(res => setTimeout(res, 800));
  // Uncomment to test error state:
  // throw new Error('Network error');
  return TEAMS;
}
```

**Evaluation checklist:**
- [ ] Components are decomposed logically — not a single 200-line component
- [ ] `useEffect` is cleaned up and dependencies are complete
- [ ] Filter is derived state — not duplicated in a separate state variable
- [ ] Keys on list items are stable IDs, not indices
- [ ] Loading and error states render correct UI, not just `console.log`

---

### Task 4 — Accessibility audit and fix

**Duration:** 30 minutes  
**Topics:** Accessibility · HTML · Audit skills

**Scenario:**  
Provide a pre-built HTML page with intentional accessibility violations. The candidate must identify, explain the user impact of, and fix each issue.

**Violations to seed in the starter file:**

| # | Violation | Expected fix |
|---|-----------|--------------|
| 1 | `<img>` with `alt="image.png"` (filename as alt) | Meaningful description or `alt=""` if decorative |
| 2 | `<div onclick="...">` used as a button | Replace with `<button>` or add `role`, `tabindex`, key handler |
| 3 | Form inputs with placeholder only, no `<label>` | Add associated `<label for>` elements |
| 4 | Low-contrast grey text on white background | Increase contrast to ≥ 4.5:1 (WCAG AA) |
| 5 | Heading jump from `<h2>` to `<h4>` | Fix to `<h3>` — no skipped levels |
| 6 | Link text reads "Click here" | Descriptive link text that names the destination |
| 7 | Icon-only button with no `aria-label` | Add `aria-label="Close"` (or equivalent) |
| 8 | Modal traps focus but has no Escape key handler | Add `keydown` listener for `Escape` to close |

**What to evaluate beyond correctness:**
- Can the candidate identify violations without relying solely on automated tools?
- Do they explain the **user impact** of each violation, not just the fix?
- Do they test with a keyboard, not just visual inspection?
- Do they use browser DevTools, axe, or WAVE to verify their fixes?

---

## Evaluation Rubrics

### Scoring scale

| Score | Label | Description |
|-------|-------|-------------|
| **4** | Strong hire | Clear mental model, proactively considers edge cases, production-quality output without prompting |
| **3** | Meets bar | Solid understanding, mostly correct code, minor gaps addressable through mentoring |
| **2** | Needs growth | Partial understanding, works only with heavy hints, output has notable issues |
| **1** | No hire | Can't explain core concepts, no working code, significant foundational gaps |

---

### Category rubrics

#### Semantic HTML (20%)

| Score | Indicators |
|-------|-----------|
| 4 | Full heading hierarchy, deep ARIA authoring knowledge, explains user impact of every choice, catches subtle issues (placeholder-only labels, ambiguous link text) |
| 3 | Uses correct landmark elements, links vs buttons correctly, basic ARIA awareness, produces accessible forms |
| 2 | Knows `<header>`, `<footer>`, `<main>` but uses `<div>` for interactive elements, placeholder-only labels, no ARIA knowledge |
| 1 | Uses only `<div>` and `<span>`, no concept of semantic meaning or accessibility |

#### CSS & Responsive Design (20%)

| Score | Indicators |
|-------|-----------|
| 4 | Intrinsic layout with `minmax()`, fluid typography via `clamp()`, theming with custom properties, no layout regressions at any viewport size, mobile-first by default |
| 3 | Can build responsive layouts with Grid and Flexbox, understands box model and specificity, uses `@media` queries correctly |
| 2 | Can style individual elements but produces brittle layouts, confuses Flexbox and Grid, relies on fixed pixel widths |
| 1 | Struggles with basic layout, can't explain box model, no responsive awareness |

#### JavaScript (25%)

| Score | Indicators |
|-------|-----------|
| 4 | Event loop understanding, performance awareness, clean async error handling, idiomatic modern JS, explains trade-offs between patterns |
| 3 | Comfortable with async/await, closures, DOM manipulation, and event handling without introducing bugs |
| 2 | Understands basic syntax but produces bugs in async code, confused by scope, reaches for `var` |
| 1 | Can't write a function that correctly handles async, no understanding of the DOM API |

#### React (25%)

| Score | Indicators |
|-------|-----------|
| 4 | Custom hooks, intentional memoization decisions with justification, clean component API design, explains reconciliation and when concurrent features apply |
| 3 | Hooks used correctly, avoids stale deps and missing keys, state lifted to appropriate level, clean component decomposition |
| 2 | Can produce a working component with help, mutates state directly, missing `useEffect` cleanup, inconsistent key usage |
| 1 | Can't write a component that manages state without bugs, confuses props and state |

#### Communication (10%)

| Score | Indicators |
|-------|-----------|
| 4 | Proactively names trade-offs before being asked, identifies alternatives unprompted, concise and precise language, asks excellent clarifying questions |
| 3 | Explains reasoning while coding, asks clarifying questions, accepts and integrates feedback gracefully |
| 2 | Codes silently, can explain after the fact but not during, defensive about corrections |
| 1 | Can't explain choices, no questions asked, rejects feedback |

---

## Score Calculator Reference

### Weighted score formula

```
Score = (HTML × 0.20) + (CSS × 0.20) + (JS × 0.25) + (React × 0.25) + (Comm × 0.10)
Percentage = (Score / 4.00) × 100
```

### Example calculations

| HTML | CSS | JS | React | Comm | Weighted | % | Verdict |
|------|-----|----|-------|------|----------|---|---------|
| 4 | 4 | 4 | 4 | 4 | 4.00 | 100% | Strong hire |
| 3 | 3 | 3 | 3 | 3 | 3.00 | 75% | Hire |
| 4 | 3 | 3 | 3 | 4 | 3.25 | 81% | Strong hire |
| 2 | 3 | 2 | 3 | 3 | 2.60 | 65% | Hire (borderline) |
| 1 | 3 | 3 | 3 | 3 | 2.65 | 66% | **No hire** (critical gap) |
| 2 | 2 | 3 | 3 | 3 | 2.65 | 66% | **No hire** (two weak areas) |

### Hire / no-hire decision tree

```
Is any single category score = 1?
  └─ YES → No hire (critical gap rule)
  └─ NO ──→ Are two or more categories scored at 2?
              └─ YES → No hire (foundational gap rule)
              └─ NO ──→ Is the weighted percentage ≥ 80%?
                          └─ YES → Strong hire
                          └─ NO ──→ Is the weighted percentage ≥ 65%?
                                      └─ YES → Hire
                                      └─ NO  → No hire
```

---

## Interviewer Tips

### Before the session

- Read through all answer guidance for questions you plan to ask. Don't evaluate on the fly — know what a strong answer looks like in advance.
- Prepare a CodeSandbox or StackBlitz starter for live coding tasks. Remove boilerplate that candidates would waste time on.
- For Task 4, build the accessible-violations page ahead of time with the seeded issues from the table above.

### During the session

- Ask "why" after every answer — not as a challenge but to distinguish understanding from recall.
- Note whether the candidate thinks aloud, asks clarifying questions, and names trade-offs proactively. This matters as much as correctness.
- For live coding, let the candidate struggle productively for 2–3 minutes before offering a hint. Note whether they get unstuck independently.
- If a candidate gives a wrong answer confidently, follow up with a concrete counterexample and observe how they respond to new information.

### After the session

- Fill in the score sheet within 30 minutes while the session is fresh.
- Note specific evidence for each score — avoid vague descriptors like "seemed shaky on CSS."
- Flag any critical gaps explicitly in your written debrief even if the overall score is above the hire threshold.

### Question selection guide by role seniority

| Role | Recommended questions | Skip |
|------|----------------------|------|
| Junior frontend (0–1 yr) | Q1–Q5, Q8–Q12, Q15–Q19, Q21–Q24 | Q6, Q7, Q13, Q14, Q20, Q25–Q27 |
| Junior–mid (1–2 yr) | Q1–Q7, Q8–Q14, Q15–Q20, Q21–Q26 | Q27 |
| Mid-level (2+ yr) | All questions | None |

---

*Guide version 1.0 · Covers: HTML5, CSS3, ES2022+, React 18*
