---
title: "Nullish Coalescing Operator in JavaScript: ?? vs ||"
description: "Learn how the nullish coalescing operator (??) works in JavaScript and why it is safer than the logical OR (||) operator for default values."
pubDate: 2026-10-23
categories: ["javascript", "typescript", "programming"]
---

When writing JavaScript or TypeScript, you often need to provide a fallback or default value when a variable is `null` or `undefined`. Traditionally, developers reached for the logical OR (`||`) operator. However, `||` has subtle pitfalls when dealing with falsy values like `0`, `false`, or `""` (empty strings).

Enter the **Nullish Coalescing Operator** (`??`).

---

## The Problem with Logical OR (`||`)

The logical OR operator evaluates the left-hand side expression and returns it if it is **truthy**. If it is **falsy** (which includes `false`, `0`, `-0`, `0n`, `""`, `null`, `undefined`, and `NaN`), it evaluates and returns the right-hand side.

```javascript
const count = 0;
const defaultCount = count || 10;
console.log(defaultCount); // 10 — Wait, I wanted 0!
```

In this example, because `0` is falsy in JavaScript, the expression falls back to `10`, even though `0` was a valid, intentional value.

---

## How Nullish Coalescing (`??`) Solves It

The nullish coalescing operator (`??`) is much more precise. It only falls back to the right-hand side if the left-hand side is strictly **`null`** or **`undefined`**.

```javascript
const count = 0;
const defaultCount = count ?? 10;
console.log(defaultCount); // 0 — Correct!
```

If the value is `0`, `false`, or an empty string `""`, `??` preserves it because those values are neither `null` nor `undefined`.

---

## Practical Examples

### 1. Function Parameters & Configuration

```javascript
function createUser(config) {
  // If timeout is undefined or null, default to 3000ms
  // But if timeout is explicitly 0, keep it 0.
  const timeout = config.timeout ?? 3000;
  return timeout;
}
```

### 2. Optional Properties

```javascript
const userSettings = {
  theme: "", // user chose empty string or cleared theme
};

// With ||: would incorrectly trigger fallback because "" is falsy
const activeTheme = userSettings.theme || "light"; 

// With ??: correctly keeps ""
const preciseTheme = userSettings.theme ?? "light"; 
console.log(preciseTheme); // ""
```

---

## Important Rule: Combining with `&&` or `||`

For safety, JavaScript prohibits directly combining `??` with `&&` or `||` without explicit parentheses to clarify precedence:

```javascript
// SyntaxError!
const x = a ?? b || c; 

// Valid
const x = (a ?? b) || c;
```

---

## Summary

- Use `||` when you want a fallback for **any falsy value** (`0`, `""`, `false`, `null`, `undefined`).
- Use `??` when you only want a fallback for **`null` or `undefined`**, preserving valid falsy values like `0` or `""`.
