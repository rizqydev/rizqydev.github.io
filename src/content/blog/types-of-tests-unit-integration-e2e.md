---
title: 'Types of Tests: Unit, Integration, & E2E Explained'
description: 'A practical breakdown of Unit Tests, Integration Tests, and End-to-End (E2E) Tests—when to use each, pros, cons, and the testing pyramid.'
pubDate: '2026-11-16'
categories: ['testing', 'software-engineering']
language: en
---

Testing is essential for building reliable software, but not all tests are created equal. Knowing when to write a unit test, integration test, or end-to-end (E2E) test saves time and prevents false confidence.

Here is the breakdown of the three core testing tiers.

---

## 1. Unit Tests

A **Unit Test** verifies a single, isolated piece of code (usually a function, method, or small class) in complete isolation from external dependencies like databases, networks, or file systems.

- **Scope**: Single function/component.
- **Speed**: Extremely fast (milliseconds).
- **Cost**: Low to write and maintain.
- **Mocks**: Heavy use of mocks and stubs for external dependencies.

```typescript
// Example: Unit testing a pure function
import { describe, it, expect } from 'vitest';
import { calculateDiscount } from './pricing';

describe('calculateDiscount', () => {
  it('applies 20% discount for premium users', () => {
    const result = calculateDiscount(100, 'premium');
    expect(result).toBe(80);
  });
});
```

---

## 2. Integration Tests

An **Integration Test** checks how multiple units or modules work together. It often involves interactions with real or in-memory databases, internal APIs, or file systems.

- **Scope**: Multiple components/modules interacting.
- **Speed**: Moderate (seconds).
- **Cost**: Medium.
- **Mocks**: Minimal; tests real integration points (e.g., API routes hitting a test database).

```typescript
// Example: Integration test hitting a database repository
import { describe, it, expect, beforeEach } from 'vitest';
import { db } from './db';
import { createUser, getUserById } from './user-service';

beforeEach(async () => {
  await db.exec('DELETE FROM users');
});

describe('User Service Integration', () => {
  it('saves and retrieves user from database', async () => {
    const userId = await createUser({ name: 'Alice', email: 'alice@example.com' });
    const user = await getUserById(userId);
    expect(user).toMatchObject({ name: 'Alice', email: 'alice@example.com' });
  });
});
```

---

## 3. End-to-End (E2E) Tests

An **End-to-End Test** simulates real user behavior in a real browser or application environment. It exercises the entire system from frontend UI down to the database and external services.

- **Scope**: Full application stack (UI, backend, database).
- **Speed**: Slow (tens of seconds to minutes).
- **Cost**: High to write, run, and maintain (brittle if UI changes often).
- **Tools**: Playwright, Cypress, Selenium.

```typescript
// Example: E2E test with Playwright
import { test, expect } from '@playwright/test';

test('user can log in and view dashboard', async ({ page }) => {
  await page.goto('https://example.com/login');
  await page.fill('#email', 'alice@example.com');
  await page.fill('#password', 'secret123');
  await page.click('button[type="submit"]');

  await expect(page).toHaveURL('https://example.com/dashboard');
  await expect(page.locator('h1')).toHaveText('Welcome, Alice');
});
```

---

## The Testing Pyramid

The Testing Pyramid guides how many tests of each type you should write:

1. **Base (Many Unit Tests)**: Fast, cheap, and pinpoint exact failures.
2. **Middle (Some Integration Tests)**: Verify modules connect and work properly.
3. **Top (Few E2E Tests)**: Verify critical user journeys work end-to-end without catching every edge case.

```
      /\
     /  \   <-- E2E Tests (Few, slow, expensive)
    /----\
   /      \  <-- Integration Tests (Moderate, balanced)
  /--------\
 /          \ <-- Unit Tests (Many, fast, cheap)
--------------
```

## Summary

- Use **Unit Tests** for business logic, math, and isolated helpers.
- Use **Integration Tests** for database queries, API endpoints, and service wiring.
- Use **E2E Tests** for critical money-making user flows (login, checkout, signup).
