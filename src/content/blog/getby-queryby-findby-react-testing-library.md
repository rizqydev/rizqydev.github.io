---
title: 'getBy vs queryBy vs findBy in React Testing Library'
description: 'A quick, practical guide to knowing exactly when to use getBy, queryBy, and findBy queries in React Testing Library.'
pubDate: '2026-11-24'
categories: ['testing', 'react', 'javascript']
language: en
---

When writing tests with React Testing Library (RTL), choosing the right query prefix (`getBy`, `queryBy`, or `findBy`) prevents flaky tests and unnecessary `waitFor` wrappers.

Here is how they differ and when to use each.

---

## Quick Comparison

| Query Prefix | No Match | 1 Match | >1 Match | Async? | Best Used For |
|---|---|---|---|---|---|
| **`getBy*`** | Throws error | Returns element | Throws error | No | Elements that should already be on the screen |
| **`queryBy*`** | Returns `null` | Returns element | Throws error | No | Asserting that an element is **not** present |
| **`findBy*`** | Throws error (after timeout) | Returns element | Throws error | Yes (Promise) | Elements that appear after an async operation |

---

## 1. `getBy*` (Default for synchronous elements)

Use `getBy*` when you expect the element to already exist in the DOM. If RTL cannot find the element, it throws an informative error immediately, failing the test without needing an explicit `expect(...)`.

```tsx
// Good: Simple element lookup
const button = screen.getByRole('button', { name: /submit/i });
expect(button).toBeEnabled();

// Redundant: getBy already throws if not found
expect(screen.getByRole('button', { name: /submit/i })).toBeInTheDocument();
```

---

## 2. `queryBy*` (Only for checking absence)

Use `queryBy*` exclusively when asserting that an element does **not** exist. Because `getBy*` throws an error when an element is missing, you cannot use it with `.not.toBeInTheDocument()`.

```tsx
// Good: Checking that element is NOT in the DOM
expect(screen.queryByRole('alert')).not.toBeInTheDocument();

// Bad: Do not use queryBy for positive assertions
// (getBy gives much better error messages when it fails)
expect(screen.queryByRole('button', { name: /submit/i })).toBeInTheDocument();
```

---

## 3. `findBy*` (For async elements)

Use `findBy*` when an element appears after an asynchronous action, such as a fetch call, timer, or delayed state update. 

`findBy*` returns a Promise that retries (default 1000ms timeout) until the element appears. It is equivalent to wrapping `getBy*` inside `waitFor()`.

```tsx
// Good: Awaiting async content
const successMessage = await screen.findByText(/profile updated/i);
expect(successMessage).toBeInTheDocument();

// Redundant: Avoid wrapping findBy in waitFor
// Bad: await waitFor(() => screen.findByText(...));
```

---

## What About Multiple Elements?

Each variant has an `*AllBy*` counterpart for multiple items:

- **`getAllBy*`**: Returns array of elements; throws if 0 found.
- **`queryAllBy*`**: Returns array of elements; returns empty array `[]` if 0 found.
- **`findAllBy*`**: Returns Promise resolving to array of elements; rejects if 0 found before timeout.

---

## Rule of Thumb

1. **Present right now?** &rarr; `screen.getBy*`
2. **Should NOT be present?** &rarr; `screen.queryBy*`
3. **Appearing asynchronously?** &rarr; `await screen.findBy*`
