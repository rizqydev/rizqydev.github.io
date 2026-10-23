# Product Requirements Document (PRD): Visual Revamp

## 1. Executive Summary

- **Problem Statement**: The current blog has an inconsistent, dated aesthetic with unoptimized layout spacing, lacking a cohesive modern identity and polished dark/light mode balance.
- **Proposed Solution**: Execute a purely visual revamp adopting a Minimalist Editorial design system using existing Astro 5 and Tailwind CSS v4 setup, emphasizing typography hierarchy, smooth theme toggling, and clean spacing without modifying routing or content.
- **Success Criteria**:
  - Achieve a 100/100 Google Lighthouse score across Performance, Accessibility, and Best Practices on mobile and desktop.
  - Zero disruption to existing URL routing (`/`, `/about`, `/blog`, `/blog/[slug]`) and markdown content fidelity.
  - Page weight (CSS + JS bundle) remains under 50KB total for the home and article pages.
  - Cumulative Layout Shift (CLS) <= 0.01 during theme switching and page transitions.

---

## 2. User Experience & Functionality

### User Personas
- **The Developer / Reader**: Wants distraction-free long-form reading, crisp code typography, effortless readability in both day and night lighting conditions, and fast load times.
- **The Author**: Wants a portfolio/blog appearance that projects professional technical taste through understated, high-craft editorial minimalism.

### User Stories & Acceptance Criteria

#### Story 1: Minimalist Editorial Aesthetics & Reading Experience
*As a reader, I want clear typography, generous line-height, and refined spacing so that reading technical articles is comfortable and distraction-free.*
- **Acceptance Criteria**:
  - Primary body text uses clean system/editorial sans/serif typography with `line-height >= 1.7` and maximum measure (`max-w-prose` / ~65–75 characters per line).
  - Code snippets render with crisp font pairing, subtle border framing, and accessible color contrast meeting WCAG AAA for text and AA for syntax tokens.
  - Post headings (`h1`-`h4`) possess a distinct mathematical scale with balanced vertical margins.

#### Story 2: Theme Switching & Contrast
*As a reader, I want seamless light and dark modes with a reliable toggle and system preference detection so that the site matches my environment.*
- **Acceptance Criteria**:
  - Default theme honors `prefers-color-scheme`.
  - Manual toggle persists user preference in `localStorage` without flash of unstyled content (FOUC).
  - Palette uses a refined neutral spectrum (e.g. Zinc/Slate: pure white `#FFFFFF` / soft off-black `#09090B` backgrounds with subtle `#27272A` borders) avoiding harsh oversaturated accents.

#### Story 3: Navigation & Page Layout Cohesion
*As a visitor, I want a clean header, minimal navigation links, and a cohesive footer across all 4 core pages so that navigation feels light and intuitive.*
- **Acceptance Criteria**:
  - Header is compact, aligned to a consistent container width (max 680px–720px for editorial focus).
  - Active states on links are subtle (subtle text color transition or underline shift, no heavy pill buttons).
  - Blog post listing displays clean date metadata, tags, and titles in a structured vertical rhythm.

### Non-Goals
- **No new features**: No search engine integration, no comment systems, no newsletter modals, and no analytics dashboards.
- **No schema or content changes**: Markdown frontmatter in `src/content/blog/` remains untouched.
- **No new heavyweight UI runtime libraries**: No React/Vue client runtimes introduced; remains pure static Astro with minimal native vanilla JS for theme toggling.

---

## 3. AI System Requirements
*Not Applicable (this project is a static content site).*

---

## 4. Technical Specifications

### Architecture Overview
- **Framework**: Astro (SSG output).
- **Styling**: Tailwind CSS v4 (`@tailwindcss/vite` & `@tailwindcss/typography`).
- **State Management**: Zero framework state. Minimal inline `<script>` for theme initialization in `<head>` to prevent FOUC.
- **Asset Handling**: Native SVG icons for theme toggle and social links; standard Astro content collections for markdown ingestion.

### Component & Layout Refactor Targets
- `src/layouts/Layout.astro`: Shell layout with global header, footer, meta tags, and inline anti-FOUC theme script.
- `src/layouts/BlogLayout.astro`: Editorial wrapper enforcing prose constraints, typography styling, and reading rhythm.
- `src/pages/index.astro`: Clean landing introduction, recent writing list, and social presence.
- `src/pages/about.astro`: Streamlined biography/experience editorial view.
- `src/pages/blog/index.astro`: Chronological post archive grouped or listed cleanly with date badges.
- `src/pages/blog/[slug].astro`: Markdown render target with typography styles and post navigation.

### Security & Privacy
- Zero third-party trackers or external CDN script injections.
- All styles compiled ahead-of-time via Tailwind CSS v4.

---

## 5. Risks & Roadmap

### Phased Rollout

#### Phase 1: Foundation (Design Tokens & Shell)
- Configure typography scale, neutral color palettes (Zinc/Slate), and anti-FOUC theme switcher script in `Layout.astro`.
- Establish standard container widths and vertical grid units.

#### Phase 2: Core Page Revamp
- Redesign `index.astro` and `about.astro` to match Minimalist Editorial standards.
- Redesign `blog/index.astro` (post listings).

#### Phase 3: Article & Typography Polish
- Polish `BlogLayout.astro` and `blog/[slug].astro` typography (`@tailwindcss/typography` custom utility classes, code block framing, inline code styles).

#### Phase 4: Validation & Quality Gate
- Run Lighthouse audits to guarantee 100/100 across mobile and desktop.
- Verify zero regression in slug URLs, markdown parsing, and build times.

### Technical Risks & Mitigations
- **Theme Flash (FOUC)**: Add small synchronous blocking script in `<head>` reading `localStorage` / `matchMedia` prior to HTML body render.
- **Tailwind v4 Typography Specificity**: Use Tailwind v4 `@theme` and `@utility` rules in `src/styles/global.css` rather than fragile inline utility overrides.
