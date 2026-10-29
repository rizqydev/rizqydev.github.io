---
title: 'Mastering useCallback in React: When (and When Not) to Use It'
description: 'Understand how useCallback memoizes function definitions, when it actually prevents unnecessary re-renders, and why React Compiler changes the game.'
pubDate: '2026-10-29'
heroImage: '/blog-placeholder-1.jpg'
categories: ['react', 'javascript', 'frontend']
language: en
---

# Mastering `useCallback` in React: When (and When Not) to Use It

In React, every time a component re-renders, all functions defined inside its body are recreated from scratch. In 99% of cases, this is completely fine because function creation in JavaScript is extremely cheap.

However, sometimes you need a function reference to remain stable across renders—most notably when passing callbacks to optimized child components wrapped in `React.memo` or when the function is a dependency in another Hook like `useEffect`.

Enter **`useCallback`**.

---

## What is `useCallback`?

`useCallback` caches a function definition between re-renders until its dependencies change.

```typescript
const memoizedCallback = useCallback(() => {
  doSomething(a, b);
}, [a, b]);
```

### How it Works Under the Hood
React stores the function and its dependency array in memory. On subsequent renders, if the dependencies in `[a, b]` have not changed (`areHookInputsEqual`), React returns the exact same function reference instead of creating a new one.

---

## When Should You Actually Use `useCallback`?

### 1. Passing Callbacks to Memoized Child Components
If you pass a callback function down to a child component wrapped in `React.memo`, passing an unstable function reference will defeat the memoization and cause the child to re-render needlessly.

```tsx
import { useState, useCallback, memo } from 'react';

const Button = memo(({ onClick }: { onClick: () => void }) => {
  console.log('Button re-rendered!');
  return <button onClick={onClick}>Click me</button>;
});

export function Parent() {
  const [count, setCount] = useState(0);

  // Without useCallback, this function is new on every render,
  // causing <Button /> to re-render even when count changes.
  const handleClick = useCallback(() => {
    console.log('Button clicked');
  }, []);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(c => c + 1)}>Increment Count</button>
      <Button onClick={handleClick} />
    </div>
  );
}
```

### 2. Functions as Dependencies in `useEffect`
If your function needs to be included in a `useEffect` dependency array, wrapping it in `useCallback` prevents infinite re-render loops or unnecessary effect triggers.

---

## Common Pitfalls: When NOT to Use It

1. **Premature Optimization:** Wrapping every single function in `useCallback` adds overhead (dependency array comparisons and memory allocation) for zero performance gain if the component is not memoized (`React.memo`).
2. **Missing Dependencies:** Forgetting to update the dependency array leads to stale closures and bugs.

---

## The Future: React Compiler
With the arrival of **React Compiler**, manual memoization with `useCallback` and `useMemo` is becoming largely automated. The compiler analyzes your code and automatically memoizes function definitions and values where appropriate, freeing you from manual dependency management.

---

## Conclusion
`useCallback` is a powerful tool when used correctly with `React.memo` or `useEffect`. Use it purposefully rather than as a default for every function in your codebase.

→ skipped: custom debounce callback hooks, add when search input throttling is explicitly requested.
