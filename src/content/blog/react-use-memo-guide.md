---
title: 'Mastering useMemo in React: Skip Expensive Calculations'
description: 'Learn how to cache calculation results between re-renders using useMemo, when it is actually needed, and why premature memoization hurts readability.'
pubDate: '2026-10-29'
heroImage: '/blog-placeholder-2.jpg'
categories: ['react', 'javascript', 'frontend']
language: en
---

# Mastering `useMemo` in React: Skip Expensive Calculations

Every time a React component re-renders, all code inside its body runs again from top to bottom. If your component performs heavy mathematical calculations, data filtering, or array transformations on every render, it can cause noticeable UI jank.

Enter **`useMemo`**.

---

## What is `useMemo`?

`useMemo` lets you cache the result of a calculation between re-renders.

```typescript
const cachedValue = useMemo(() => {
  return computeExpensiveValue(a, b);
}, [a, b]);
```

### How it Works
React stores the computed result and the dependency array in memory. On subsequent re-renders, if the dependencies in `[a, b]` haven't changed, React skips recalculating and returns the cached result.

---

## Practical Example: Filtering a Large List

Imagine filtering a list of 10,000 items as the user types into a search input:

```tsx
import { useState, useMemo } from 'react';

export function ProductList({ products }: { products: { id: number; name: string; price: number }[] }) {
  const [query, setQuery] = useState('');
  const [theme, setTheme] = useState('light'); // Unrelated state

  // Without useMemo, filtering runs on EVERY render (even when toggling theme).
  // With useMemo, filtering only runs when `products` or `query` changes.
  const filteredProducts = useMemo(() => {
    console.log('Filtering products...');
    return products.filter(p => p.name.toLowerCase().includes(query.toLowerCase()));
  }, [products, query]);

  return (
    <div className={theme}>
      <button onClick={() => setTheme(t => t === 'light' ? 'dark' : 'light')}>
        Toggle Theme
      </button>
      <input
        value={query}
        onChange={e => setQuery(e.target.value)}
        placeholder="Search products..."
      />
      <ul>
        {filteredProducts.map(p => (
          <li key={p.id}>{p.name} - ${p.price}</li>
        ))}
      </ul>
    </div>
  );
}
```

---

## When SHOULD You Use `useMemo`?

1. **Truly Expensive Calculations:** The calculation takes noticeable CPU time (e.g., looping through thousands of objects, complex data transformations).
2. **Referential Equality for Objects/Arrays:** When passing an object or array as a dependency to `useEffect` or as a prop to a memoized child component (`React.memo`), memoizing it prevents accidental infinite loops or extra child renders.

---

## When NOT to Use `useMemo`

- **Simple calculations:** Adding two numbers or filtering an array of 5 items is faster for JavaScript to compute than it is for React to check dependency arrays and manage cache slots.
- **As a default:** Memoization is not free; it consumes memory and adds code complexity.

---

## The Future: React Compiler
Just like `useCallback`, manual memoization with `useMemo` is increasingly handled automatically by the **React Compiler**, which analyzes reactive dependencies and inserts memoization slots at build time.

---

## Conclusion
`useMemo` is a powerful performance tool when applied to truly heavy computations or reference stability. Use it with intention, not as a blanket rule across your entire codebase.

→ skipped: custom memo cache size limits, add when memory pressure tuning is required in low-spec devices.
