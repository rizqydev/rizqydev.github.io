# Dark Mode Color Adjustments

Date: 20260812

## Goal
- Headings/body text that is currently hardcoded `text-gray-900` / `text-gray-500` / `text-gray-600` / `text-gray-700` is still effectively black on dark backgrounds. Add `dark:` variants so the text becomes light when dark mode is active.
- The article cards in `src/pages/blog/index.astro` are `bg-white`. Make them adapt to dark mode.

## Scope (overrides earlier scope decision)
The previous dark-mode plan deliberately avoided modifying page components. The user has now explicitly asked for font color + article background fixes. This plan covers those changes.

## Files to Change
- `src/pages/index.astro`
- `src/pages/blog/index.astro`
- `src/pages/blog/[slug].astro`

No layout/global.css changes needed — Tailwind's `dark` variant is already enabled.

## Changes

### 1. `src/pages/index.astro`
- Hero `<h1>` and `<h2>` `text-gray-900` → add `dark:text-gray-100`.
- Hero `<p>` `text-gray-600` → add `dark:text-gray-300`.
- Latest-post article card `bg-white border-gray-200` → add `dark:bg-gray-800 dark:border-gray-700`.
- Latest-post date `text-gray-500` → add `dark:text-gray-400`.
- Latest-post `<h3>` `text-gray-900` → add `dark:text-gray-100`.
- Latest-post description `text-gray-600` → add `dark:text-gray-300`.
- "Recent Posts" `<h2>` `text-gray-900` → add `dark:text-gray-100`.
- Other-post article cards `bg-white border-gray-200` → add `dark:bg-gray-800 dark:border-gray-700`.
- Other-post date `text-gray-500` → add `dark:text-gray-400`.
- Other-post `<h3>` `text-gray-900` → add `dark:text-gray-100`.
- Other-post description `text-gray-600` → add `dark:text-gray-300`.
- Empty-state `<p>` `text-gray-600` → add `dark:text-gray-300`.

### 2. `src/pages/blog/index.astro`
- Page `<h1>` `text-gray-900` → add `dark:text-gray-100`.
- Intro `<p>` `text-gray-600` → add `dark:text-gray-300`.
- Each post article card `bg-white border-gray-200` → add `dark:bg-gray-800 dark:border-gray-700`.
- Each post date `text-gray-500` → add `dark:text-gray-400`.
- Each post `<h2>` `text-gray-900` → add `dark:text-gray-100`.
- Each post description `text-gray-600` → add `dark:text-gray-300`.
- Empty-state `<p>` `text-gray-600` → add `dark:text-gray-300`.

### 3. `src/pages/blog/[slug].astro`
- Back link `text-indigo-600` → add `dark:text-indigo-400`; hover already indigo.
- Dot separator is decorative (`<span>•</span>`), color `text-gray-500` → add `dark:text-gray-400`.
- Article date `text-gray-500` → add `dark:text-gray-400`.
- Article `<h1>` `text-gray-900` → add `dark:text-gray-100`.
- Article description `text-gray-600` → add `dark:text-gray-300`.
- Footer `<a>` `text-indigo-600` → add `dark:text-indigo-400`.
- Footer `border-t` → add `dark:border-gray-700`.
- Note: the hero `<div class="aspect-video bg-gradient-to-br from-indigo-500 to-purple-600 ...">` is intentionally a colored gradient — leave unchanged.

## Light-mode default
Every change keeps the original light class as the default and only adds `dark:` variants. No regression for light theme.

## Out of scope
- `prose prose-lg prose-indigo` styling on `[slug].astro` content body — typography plugin styling is not touched.
- Header / footer / nav in `BlogLayout.astro` — already themed in the previous plan.
- Any styling beyond color (spacing, layout) — left untouched per project rules.

## Verification
- Manually check `npm run dev`: headings, dates, descriptions, and article cards switch correctly when dark mode toggles.
- Per project rules: do NOT run `npm run build` or any terminal checks after changing code.