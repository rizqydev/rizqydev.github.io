# Home Page Rewrite

Date: 20260818

## Goal
Replace the current `src/pages/index.astro` content with a personal intro:
1. Hero block at the top with a title `Hello, I'm Rizqy` and a short Lorem Ipsum paragraph below.
2. Two "latest posts" lists below the hero — one for English posts, one for Indonesian posts — each showing the 5 most recent posts in that language with title, date, and a link to the post.

## Why a `language` field
The blog schema (`src/content.config.ts`) currently has no `language` field, but the post bodies are written in either English or Indonesian. A dedicated `language` field is the cleanest, most explicit signal: it survives filename changes, content rewrites, and is trivially filterable. Alternatives (category reuse, filename prefix, content heuristics) were rejected for being implicit or fragile. The user is OK with adding a new schema field.

## Files to Change
- `src/content.config.ts` — add `language` to blog schema
- `src/content/blog/first-post.md` — set `language: en`
- `src/content/blog/typescript-guide.md` — set `language: en`
- `src/content/blog/the-difference-betwen-union-and-union-all-in-postgres.md` — set `language: en`
- `src/pages/index.astro` — full rewrite (hero + two latest-posts lists)

`src/layouts/BlogLayout.astro`, `src/pages/blog/index.astro`, and `src/pages/blog/[slug].astro` are NOT changed.

## Schema

### `src/content.config.ts`
Add to the blog `schema` z.object (alphabetical-ish, near `categories`):
```ts
language: z.enum(['en', 'id']).default('en'),
```
Default `en` keeps existing posts (which are all English) valid. New Indonesian posts will need to set `language: id` explicitly.

## Seed language on existing posts

### `src/content/blog/first-post.md`
Add to frontmatter:
```yaml
language: en
```

### `src/content/blog/typescript-guide.md`
Add to frontmatter:
```yaml
language: en
```

### `src/content/blog/the-difference-betwen-union-and-union-all-in-postgres.md`
Add to frontmatter:
```yaml
language: en
```

## UI: home page (`src/pages/index.astro`)

### Frontmatter
- Fetch all posts with `getCollection('blog')`, sort by `pubDate` desc (same as today).
- Split into two arrays:
  - `englishPosts = posts.filter(p => (p.data.language ?? 'en') === 'en').slice(0, 5)`
  - `indonesianPosts = posts.filter(p => p.data.language === 'id').slice(0, 5)`
- Keep `BlogLayout` import and `title`/`description` props (must not be dropped — recurring regression risk per user memory).

### Markup structure
1. **Hero section** (top of page):
   ```astro
   <section class="text-center py-16">
     <h1 class="text-4xl md:text-5xl font-bold text-gray-900 mb-4 dark:text-gray-100">
       Hello, I'm Rizqy
     </h1>
     <p class="text-lg text-gray-600 max-w-2xl mx-auto dark:text-gray-300">
       Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
     </p>
   </section>
   ```
   - Tailwind classes match the existing hero pattern (text sizes, dark-mode variants) so the page stays visually consistent.

2. **Two-column latest posts** section (below hero, English and Indonesian side-by-side):
   ```astro
   <section class="mb-12">
     <div class="grid md:grid-cols-2 gap-6">
       <!-- English column -->
       <div>
         <h2 class="text-2xl font-semibold text-gray-900 mb-6 dark:text-gray-100">Latest Articles (English)</h2>
         {englishPosts.length === 0 ? (
           <p class="text-gray-600 dark:text-gray-300">No English posts yet.</p>
         ) : (
           <ul class="space-y-4">
             {englishPosts.map((post) => (
               <li class="bg-white rounded-xl shadow-sm border border-gray-200 p-4 hover:shadow-md transition dark:bg-gray-800 dark:border-gray-700">
                 <a href={`/blog/${post.id}`} class="block">
                   <div class="flex items-center gap-2 text-sm text-gray-500 mb-1 dark:text-gray-400">
                     <time datetime={post.data.pubDate.toISOString()}>
                       {post.data.pubDate.toLocaleDateString('en-US', { year: 'numeric', month: 'long', day: 'numeric' })}
                     </time>
                   </div>
                   <h3 class="text-lg font-bold text-gray-900 hover:text-indigo-600 transition dark:text-gray-100">
                     {post.data.title}
                   </h3>
                 </a>
               </li>
             ))}
           </ul>
         )}
       </div>

       <!-- Indonesian column -->
       <div>
         <h2 class="text-2xl font-semibold text-gray-900 mb-6 dark:text-gray-100">Artikel Terbaru (Bahasa Indonesia)</h2>
         {indonesianPosts.length === 0 ? (
           <p class="text-gray-600 dark:text-gray-300">Belum ada artikel Bahasa Indonesia.</p>
         ) : (
           <ul class="space-y-4">
             {indonesianPosts.map((post) => (
               <li class="bg-white rounded-xl shadow-sm border border-gray-200 p-4 hover:shadow-md transition dark:bg-gray-800 dark:border-gray-700">
                 <a href={`/blog/${post.id}`} class="block">
                   <div class="flex items-center gap-2 text-sm text-gray-500 mb-1 dark:text-gray-400">
                     <time datetime={post.data.pubDate.toISOString()}>
                       {post.data.pubDate.toLocaleDateString('id-ID', { year: 'numeric', month: 'long', day: 'numeric' })}
                     </time>
                   </div>
                   <h3 class="text-lg font-bold text-gray-900 hover:text-indigo-600 transition dark:text-gray-100">
                     {post.data.title}
                   </h3>
                 </a>
               </li>
             ))}
           </ul>
         )}
       </div>
     </div>
   </section>
   ```

### Layout decisions
- English and Indonesian lists are rendered side-by-side inside a single `<section>` using `grid md:grid-cols-2 gap-6` — on mobile they stack vertically (`md:` breakpoint), on tablet/desktop they appear as two parallel columns.
- Both lists are `<ul>` of `<li>` cards — simpler than the current 2-column grid and reads as a "list of 5" as requested.
- Only title + date per post (no description, no hero image) — the user asked for a list, not a full card grid.
- Date formatting uses `en-US` locale for the English list and `id-ID` locale for the Indonesian list (e.g. "May 8, 2025" vs "8 Mei 2025").
- Empty-state copy is shown per column so adding the first Indonesian post later does not look broken.
- The "Latest Post" highlight that existed in the current `index.astro` is removed — the new structure (two per-language columns of 5) replaces it.

## Out of scope
- The `description` of each post (left out of the home list to keep it compact; still shown on `/blog` and the post page).
- Changing `/blog` index page or the post page layout.
- A "View all" link to `/blog` from each section — not requested; can be added in a follow-up.
- Migrating Indonesian-only posts that may exist elsewhere — there are none in the repo today.
- i18n routing (e.g. `/id/...`) — out of scope; this is just a home-page grouping by language.

## Verification
- Manually check `npm run dev`:
  - Hero shows `Hello, I'm Rizqy` and the Lorem Ipsum paragraph.
  - Below the hero, on desktop/tablet (>= md breakpoint) the two lists appear as parallel columns: "Latest Articles (English)" on the left, "Artikel Terbaru (Bahasa Indonesia)" on the right. On mobile they stack vertically.
  - English list shows up to 5 posts (currently 3 exist, all English), newest first, each linking to `/blog/<slug>`. Dates are in `en-US` format (e.g. "May 8, 2025").
  - Indonesian column shows the empty-state copy `Belum ada artikel Bahasa Indonesia.`.
  - Dark mode still works (every class has a `dark:` variant).
- Per project rules: do NOT run `npm run build` or any terminal checks after changing code.
