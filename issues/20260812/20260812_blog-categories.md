# Blog Categories

Date: 20260812

## Goal
Add a `categories` field to blog posts. Categories are displayed:
1. On each post page, below the post title (above the description).
2. On `src/pages/blog/index.astro`, a row of category pills/chips above the intro line "All my blog posts in one place.".

Clicking a category pill on `/blog` filters the visible post list to posts in that category. Clicking the active pill again (or an "All" pill) clears the filter.

## Why client-side filtering
Astro builds this site statically (`astro build` with `getStaticPaths`). Adding one page per category (`/blog/category/[slug]`) would work for SSG, but the user asked for filtering on the existing `/blog` page. The simplest and most consistent approach: render all posts server-side, then filter with a small inline `<script>` on the client. This keeps `/blog` a single static page and avoids generating N category routes.

## Files to Change
- `src/content.config.ts`
- `src/content/blog/first-post.md`
- `src/content/blog/typescript-guide.md`
- `src/pages/blog/index.astro`
- `src/pages/blog/[slug].astro`

## Schema

### `src/content.config.ts`
Add to the blog `schema` z.object:
```ts
categories: z.array(z.string()).default([]),
```
Default `[]` keeps existing posts valid.

## Seed categories in existing posts

### `src/content/blog/first-post.md`
```yaml
categories: ['astro', 'meta']
```

### `src/content/blog/typescript-guide.md`
```yaml
categories: ['typescript', 'javascript']
```

## UI: post page (`src/pages/blog/[slug].astro`)

Under the article `<h1>` and above the description `<p>`, render a category row:
```astro
{post.data.categories && post.data.categories.length > 0 && (
  <div class="flex flex-wrap gap-2 mb-4">
    {post.data.categories.map((cat) => (
      <a
        href={`/blog?category=${encodeURIComponent(cat)}`}
        class="inline-block px-3 py-1 text-xs font-medium rounded-full bg-indigo-50 text-indigo-700 hover:bg-indigo-100 transition dark:bg-indigo-900/40 dark:text-indigo-300 dark:hover:bg-indigo-900/60"
      >
        {cat}
      </a>
    ))}
  </div>
)}
```
- Pills link back to `/blog?category=<cat>` so the index page can pick up the filter from the URL on load.
- Dark-mode variants match the existing palette.

## UI: index page (`src/pages/blog/index.astro`)

### Frontmatter
- After fetching `posts`, compute `categories = Array.from(new Set(posts.flatMap(p => p.data.categories ?? []))).sort()`.
- Build a `categoriesWithCount` array: `[{ name, count }]` (only categories with count > 0).
- Add an optional helper to read `Astro.url.searchParams.get('category')` for SSR-aware initial highlight (since this is SSG, `Astro.url` is the canonical URL — `?category=...` is not part of static paths; this read will only work in dev. Plan below uses client-side reading as the primary path).

### Markup
1. Add a pill row above the intro `<p>`:
```astro
<div id="category-bar" class="flex flex-wrap gap-2 mb-6" role="tablist" aria-label="Filter by category">
  <button
    type="button"
    data-category=""
    class="category-pill inline-block px-3 py-1 text-xs font-medium rounded-full bg-indigo-600 text-white dark:bg-indigo-500"
  >All</button>
  {categories.map(({ name, count }) => (
    <button
      type="button"
      data-category={name}
      class="category-pill inline-block px-3 py-1 text-xs font-medium rounded-full bg-indigo-50 text-indigo-700 hover:bg-indigo-100 transition dark:bg-indigo-900/40 dark:text-indigo-300 dark:hover:bg-indigo-900/60"
    >
      {name} <span class="opacity-70">({count})</span>
    </button>
  ))}
</div>
```

2. Each `<article>` in the posts list gets `data-categories="<comma-separated categories>"` (lower-cased) so the script can filter:
```astro
<article
  data-categories={(post.data.categories ?? []).join(',').toLowerCase()}
  class="post-card ... (existing classes)"
>
```

3. Add an empty-filter state `<p>` below the post list (only visible when no posts match). Hidden by default.

### Inline filter script
Inside `src/pages/blog/index.astro`, append an `is:inline` script before `</BlogLayout>`:
```html
<script is:inline>
(function () {
  const bar = document.getElementById('category-bar');
  const cards = document.querySelectorAll('.post-card');
  const empty = document.getElementById('no-results');
  if (!bar) return;

  const pills = bar.querySelectorAll('.category-pill');
  const baseActive = pills.length ? pills[0].className : '';

  function applyFilter(cat) {
    cat = (cat || '').toLowerCase();
    pills.forEach((p) => {
      const match = (p.getAttribute('data-category') || '').toLowerCase() === cat;
      p.className = match
        ? 'category-pill inline-block px-3 py-1 text-xs font-medium rounded-full bg-indigo-600 text-white dark:bg-indigo-500'
        : 'category-pill inline-block px-3 py-1 text-xs font-medium rounded-full bg-indigo-50 text-indigo-700 hover:bg-indigo-100 transition dark:bg-indigo-900/40 dark:text-indigo-300 dark:hover:bg-indigo-900/60';
    });

    let visible = 0;
    cards.forEach((card) => {
      const cats = (card.getAttribute('data-categories') || '').split(',').filter(Boolean);
      const show = !cat || cats.includes(cat);
      card.style.display = show ? '' : 'none';
      if (show) visible++;
    });
    if (empty) empty.style.display = visible === 0 ? '' : 'none';

    const url = new URL(window.location.href);
    if (cat) url.searchParams.set('category', cat);
    else url.searchParams.delete('category');
    history.replaceState(null, '', url.toString());
  }

  // Initial state from URL
  const initial = new URL(window.location.href).searchParams.get('category') || '';
  applyFilter(initial);

  bar.addEventListener('click', (e) => {
    const btn = e.target.closest('.category-pill');
    if (!btn) return;
    applyFilter(btn.getAttribute('data-category') || '');
  });
})();
</script>
```

## Out of scope
- Per-category pages (`/blog/category/[slug]`) — not requested; client-side filtering on `/blog` covers the ask.
- `astro.config.mjs` changes.
- Categories on the home page (`src/pages/index.astro`) — not requested. Could be added in a follow-up.
- Restyling — only the new pill elements get Tailwind classes; existing colors/spacing untouched.

## Verification
- Manually check `npm run dev`:
  - Posts show category pills below their title.
  - `/blog` shows a category bar with counts.
  - Clicking a category filters the list; the active pill stays highlighted; URL updates with `?category=...`.
  - Clicking "All" or the active pill clears the filter.
  - Deep-linking to `/blog?category=astro` shows the filter applied on initial render.
- Per project rules: do NOT run `npm run build` or any terminal checks after changing code.