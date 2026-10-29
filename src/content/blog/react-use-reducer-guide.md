---
title: 'Mastering useReducer in React: When to Graduate from useState'
description: 'Learn how to manage complex state logic and multiple sub-values cleanly in React using the useReducer hook.'
pubDate: '2026-10-29'
heroImage: '/blog-placeholder-2.jpg'
categories: ['react', 'javascript', 'frontend']
language: en
---

# Mastering useReducer in React: When to Graduate from `useState`

When building React applications, `useState` is usually the first hook we reach for. However, as components grow to handle multiple related state variables, complex conditional updates, or next-state calculations dependent on previous state, `useState` can become messy and error-prone.

Enter **`useReducer`**. Inspired by Redux, `useReducer` gives you predictable state transitions by centralizing state logic into a single reducer function.

---

## When to Use `useReducer`

You should consider using `useReducer` instead of `useState` when:
1. **You have complex state logic** involving multiple sub-values (e.g., form wizards, shopping carts, multi-step filters).
2. **The next state depends on the previous state** in intricate ways across multiple fields.
3. **You want clearer debugging** where every state change is triggered by an explicit named `action`.

---

## The Signature of `useReducer`

According to React's core definition:

```typescript
const [state, dispatch] = useReducer(reducer, initialArg, init);
```

- **`reducer`**: A pure function `(state, action) => newState` that specifies how the state updates.
- **`initialArg`**: The initial state (or argument passed to an optional `init` function).
- **`init`**: (Optional) A lazy initialization function.
- **`state`**: The current state value.
- **`dispatch`**: A function that lets you "dispatch" actions to trigger state updates.

---

## Practical Example: Managing a Complex Form / Cart State

Let's look at a shopping cart managed with `useReducer`:

```tsx
import { useReducer } from 'react';

// 1. Define state type / shape
type CartState = {
  items: { id: number; name: string; quantity: number }[];
  couponCode: string | null;
  discount: number;
};

// 2. Define action types using a discriminated union
type CartAction =
  | { type: 'ADD_ITEM'; item: { id: number; name: string } }
  | { type: 'REMOVE_ITEM'; id: number }
  | { type: 'APPLY_COUPON'; code: string; discount: number }
  | { type: 'CLEAR_CART' };

// 3. Write the reducer function
function cartReducer(state: CartState, action: CartAction): CartState {
  switch (action.type) {
    case 'ADD_ITEM': {
      const existingIndex = state.items.findIndex(i => i.id === action.item.id);
      if (existingIndex > -1) {
        const updatedItems = [...state.items];
        updatedItems[existingIndex].quantity += 1;
        return { ...state, items: updatedItems };
      }
      return {
        ...state,
        items: [...state.items, { ...action.item, quantity: 1 }],
      };
    }
    case 'REMOVE_ITEM':
      return {
        ...state,
        items: state.items.filter(i => i.id !== action.id),
      };
    case 'APPLY_COUPON':
      return {
        ...state,
        couponCode: action.code,
        discount: action.discount,
      };
    case 'CLEAR_CART':
      return { items: [], couponCode: null, discount: 0 };
    default:
      throw new Error(`Unhandled action type`);
  }
}

// 4. Component usage
export function ShoppingCart() {
  const [state, dispatch] = useReducer(cartReducer, {
    items: [],
    couponCode: null,
    discount: 0,
  });

  return (
    <div>
      <h2>Shopping Cart ({state.items.length} items)</h2>
      <button
        onClick={() =>
          dispatch({ type: 'ADD_ITEM', item: { id: 1, name: 'React Book' } })
        }
      >
        Add React Book
      </button>
      {state.items.map(item => (
        <div key={item.id}>
          {item.name} x {item.quantity}
          <button onClick={() => dispatch({ type: 'REMOVE_ITEM', id: item.id })}>
            Remove
          </button>
        </div>
      ))}
    </div>
  );
}
```

---

## Combining `useReducer` with `useContext`

One of the most powerful patterns in React is pairing `useReducer` with `useContext`. This gives you a lightweight, Redux-like global state management solution without installing external libraries:

- Store the `state` and `dispatch` in a Context Provider.
- Consume `dispatch` anywhere in the component tree to trigger updates without prop drilling.

---

## Conclusion
`useReducer` keeps your state transitions organized, predictable, and testable. When `useState` starts feeling cluttered with complex object updates or multiple flags, upgrade to `useReducer`.

→ skipped: custom state logging middleware, add when debugging complex distributed state updates in production.
