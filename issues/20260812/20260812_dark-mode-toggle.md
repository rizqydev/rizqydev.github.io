# Dark Mode Toggle

Date: 20260812

## Goal
Add a dark mode toggle button in the top-right corner of the site header that switches the site between light and dark themes.

## Files to Change
- `src/styles/global.css`
- `src/layouts/BlogLayout.astro`

## Approach
The site uses Tailwind CSS v4. All themed colors are hardcoded Tailwind utility classes (e.g. `bg-white`, `text-gray-900`, `bg-gray-50`) spread across `BlogLayout.astro` and the page components (`index.astro`, `blog/index.astro`, `blog/[slug].astro`).

Dark mode will be implemented with Tailwind's class-based dark variant. The toggle sets a `dark` class on the `<html>` element; all existing light colors are then overridden with `dark:` variants inside the layout.

### 1. `src/styles/global.css`
- Enable class-based dark mode for Tailwind v4:
  ```css
  @custom-variant dark (&:where(.dark, .dark *));
  ```
- Keep existing `@import "tailwindcss";` and `@plugin "@tailwindcss/typography";` lines.

### 2. `src/layouts/BlogLayout.astro`
- Add an inline script in `<head>` that runs before paint (prevents flash of wrong theme):
  - Reads `localStorage.theme`; falls back to `prefers-color-scheme: dark`.
  - Adds/removes the `dark` class on `<html>`.
  - Persists the chosen theme to `localStorage`.
- Add a toggle button in the `<nav>` (top-right area, after the nav links):
  - Contains a sun icon (visible in dark mode) and a moon icon (visible in light mode).
  - On click, flips the theme and saves it.
- Add `dark:` variants to `body`, `header`, `footer`, and nav link/heading colors so the layout adapts. Light colors remain the default.
- Do NOT modify the page components (`index.astro`, `blog/index.astro`, `blog/[slug].astro`) — card/hero colors are out of scope for this change per project rules (only the layout chrome is themed).

## Behavior
- Toggle lives in the top-right of the header.
- Clicking toggles `dark` class on `<html>`.
- Preference persisted in `localStorage`, so it survives page reloads and navigation.
- First-time visitors get system preference; no flash of incorrect theme.

## Verification
- Manually check `npm run dev` (Astro dev server): toggle switches page between light/dark, preference persists on reload, and no flash on initial load.
- Per project rules: do NOT run `npm run build` or any terminal checks after changing code.