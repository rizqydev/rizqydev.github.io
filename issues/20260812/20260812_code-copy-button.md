# Code Block Copy Button

Date: 20260812

## Goal
Add a "Copy" button to every code block on blog post pages so visitors can copy the code to their clipboard with one click. The button must work for both light and dark modes and be unobtrusive (top-right corner, subtle styling, only appears on hover for desktop and is always visible on touch / coarse pointers).

## Files to Change
- `src/pages/blog/[slug].astro` — only file touched.

No global CSS, layout, or config changes needed.

## Why this scope
- The blog content is rendered inside the `prose` wrapper on `[slug].astro`. Astro's built-in Shiki integration wraps every markdown fence in a `<pre>` (with class `astro-code`) containing a `<code>` element. The script needs to run after the page renders and decorate those nodes.
- A single inline `is:inline` script is enough — no new component, no Astro plugin, no extra build step.
- No global.css change is required because the button styles can use Tailwind classes (Tailwind is already configured globally).

## Behavior
- On page load, find every `<pre>` inside the article's `prose` wrapper (and also any `<pre>` not inside `prose` if present).
- Append a `<button>` to the top-right of each `<pre>`:
  - Label: "Copy" by default; changes to "Copied!" for ~1.5s after click.
  - On click: write `pre.innerText` (or `pre.textContent`) to the clipboard via `navigator.clipboard.writeText()`. Fallback to `document.execCommand('copy')` on a hidden `<textarea>` for older browsers / insecure contexts (http://localhost is fine, but keep a fallback for robustness).
  - On error: briefly show "Failed".
- The button is hidden by default and revealed on hover/focus-within (desktop). On coarse pointers (touch), it is always visible so it remains reachable. Implementation uses `@media (hover: hover) and (pointer: fine)` to gate the hover-reveal; outside that media query the button stays visible.

## Markup produced by the script
For each `<pre>`:
```html
<button type="button" class="copy-code-btn ..." aria-label="Copy code">
  <span class="copy-code-label">Copy</span>
</button>
```
The button is `position: absolute` inside a `<pre>` that becomes `position: relative`. Astro's Shiki `<pre>` already renders with explicit padding, so the absolute-positioned button will sit cleanly in the top-right.

## Tailwind classes on the button
```
absolute top-2 right-2 z-10
px-2 py-1 text-xs font-medium rounded
bg-gray-100 text-gray-700 hover:bg-gray-200
dark:bg-gray-700 dark:text-gray-200 dark:hover:bg-gray-600
transition
opacity-0 group-hover:opacity-100
[.copy-code-btn-visible_&]:opacity-100
md:opacity-0 md:group-hover:opacity-100
```
A simpler reliable approach: use raw CSS via the `is:global` block or just a `<style is:global>` inside the script.

To avoid pulling in a Tailwind group/peer ancestor wrapper for every `<pre>`, the plan uses a `<style is:global>` block in the same `<script>` block that targets `.copy-code-btn` directly:
```css
.copy-code-btn {
  position: absolute;
  top: 0.5rem;
  right: 0.5rem;
  z-index: 10;
  padding: 0.25rem 0.5rem;
  font-size: 0.75rem;
  font-weight: 500;
  border-radius: 0.375rem;
  background: #f3f4f6;
  color: #374151;
  border: 1px solid #e5e7eb;
  cursor: pointer;
  opacity: 0;
  transition: opacity 0.15s ease, background 0.15s ease;
}
.copy-code-btn:hover { background: #e5e7eb; }
.copy-code-btn:focus-visible { opacity: 1; outline: 2px solid #6366f1; outline-offset: 2px; }
.copy-code-btn[data-state="copied"] { background: #dcfce7; color: #166534; }
.copy-code-btn[data-state="failed"] { background: #fee2e2; color: #991b1b; }
.dark .copy-code-btn { background: #374151; color: #e5e7eb; border-color: #4b5563; }
.dark .copy-code-btn:hover { background: #4b5563; }
.dark .copy-code-btn[data-state="copied"] { background: #064e3b; color: #6ee7b7; }
.dark .copy-code-btn[data-state="failed"] { background: #7f1d1d; color: #fecaca; }
@media (hover: hover) and (pointer: fine) {
  pre:hover .copy-code-btn,
  pre:focus-within .copy-code-btn { opacity: 1; }
}
@media not (hover: hover) {
  .copy-code-btn { opacity: 1; }
}
```
Per project rules ("Don't modify styling. Ignore the neatness of the code and focus on its functionality.") — keep the styles functional. The button must be visible somehow to be useful, so the hover/coarse-pointer logic is necessary functionality, not aesthetic polish.

## Position relative
Astro's Shiki output `<pre>` does not always have `position: relative`. The script sets `style.position = 'relative'` directly on each `<pre>` before appending the button. Functionally required for the absolute button to anchor.

## The script (placed inside the page, before `</BlogLayout>`)
```html
<script is:inline>
(function () {
  function makeBtn() {
    const b = document.createElement('button');
    b.type = 'button';
    b.className = 'copy-code-btn';
    b.setAttribute('aria-label', 'Copy code to clipboard');
    b.innerHTML = '<span class="copy-code-label">Copy</span>';
    return b;
  }

  function attach(pre) {
    if (pre.querySelector(':scope > .copy-code-btn')) return;
    if (!pre.style.position) pre.style.position = 'relative';
    const btn = makeBtn();
    const label = btn.querySelector('.copy-code-label');
    let resetTimer;

    async function copyText() {
      const text = pre.innerText;
      try {
        if (navigator.clipboard && window.isSecureContext) {
          await navigator.clipboard.writeText(text);
        } else {
          const ta = document.createElement('textarea');
          ta.value = text;
          ta.style.position = 'fixed';
          ta.style.opacity = '0';
          document.body.appendChild(ta);
          ta.select();
          document.execCommand('copy');
          document.body.removeChild(ta);
        }
        btn.dataset.state = 'copied';
        label.textContent = 'Copied!';
      } catch (e) {
        btn.dataset.state = 'failed';
        label.textContent = 'Failed';
      }
      clearTimeout(resetTimer);
      resetTimer = setTimeout(() => {
        btn.removeAttribute('data-state');
        label.textContent = 'Copy';
      }, 1500);
    }

    btn.addEventListener('click', copyText);
    pre.appendChild(btn);
  }

  function init() {
    document.querySelectorAll('article pre').forEach(attach);
  }

  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', init);
  } else {
    init();
  }
})();
</script>
```

## Where in `[slug].astro`
Append the inline `<script>` and the `<style is:global>` block just before `</BlogLayout>`, after the existing `<article>` content. Single file change.

## Out of scope
- Copy buttons on the blog index page or home page (no code blocks render there).
- A language label ("ts", "bash", etc.) — Shiki already adds a language class on the `<pre>`. Showing it as a label can be a follow-up.
- A11y enhancements beyond `aria-label` and `focus-visible` ring — not requested.

## Verification
- Manually check `npm run dev`:
  - Each code block on a post page has a Copy button in its top-right corner.
  - Hovering (or focusing) a code block reveals the button on desktop; on touch the button is always visible.
  - Clicking copies the code, the button briefly shows "Copied!", then reverts to "Copy".
  - Light and dark mode both render the button legibly.
- Per project rules: do NOT run `npm run build` or any terminal checks after changing code.