# Canva Vault Design

**Date:** 2026-04-24

**Goal**

Build a single-file static reference site for Canva keyboard shortcuts that works by opening `index.html` directly in a browser, stays deployable anywhere without a build step, and balances strong search/share utility with a polished visual treatment guided by the supplied Figma file.

**Scope**

- One output file: `index.html`
- Plain HTML, CSS, and vanilla JS only
- All CSS in one `<style>` block
- All JS in one `<script>` block at end of `<body>`
- Shortcut dataset stored as a JS constant array in the page
- Strict shortcut content source: Canva help page only
- No frameworks, no bundlers, no package installs, no external runtime dependencies besides Google Fonts import

**Non-Goals**

- No CMS or external data fetch at runtime
- No analytics, backend, or ad network integration
- No inferred or guessed shortcuts outside the Canva help page
- No multi-page architecture

## Requirements Summary

The page must include:

- At least 80 shortcut entries if the Canva help page provides that many; otherwise include the full strict-source dataset without invention
- Sections for `Basic`, `Elements`, `Text`, `Video`, `Zoom`, `Page view`, `Comments`, and `Desktop app`
- Per-entry fields: `action`, `mac`, `windows`, `category`, `tags`
- Real-time search with match highlighting and `Escape` to clear
- OS toggle with Mac/Windows emphasis and platform detection fallback to Mac
- Sticky section headers during scroll
- Copy affordance on each row that copies plain text summary
- Dark theme for screen, clean two-column print layout for paper/PDF
- Share button that copies current URL
- PDF button that calls `window.print()`
- SEO/share tags and JSON-LD payload
- Ad placeholder slots, including mobile-only inline insertion between sections 3 and 4

## Source of Truth

Shortcut content will be collected from `https://www.canva.com/help/canva-keyboard-shortcuts/` only.

Implications:

- If the page lists fewer than 80 unique shortcuts, the implementation should preserve strict accuracy rather than inventing entries to hit the count.
- Terminology from Canva should be preserved unless a small normalization is needed for consistency in labels or tags.
- Mac and Windows values should reflect Canva’s published notation, normalized only enough to render consistently as key pills.

## Information Architecture

The page structure should be:

1. Document head with SEO/share metadata and JSON-LD
2. Hero/header area with brand name `Canva Vault`, SEO title, and short explanatory copy
3. Top controls row containing:
   - Search input
   - OS toggle
   - Share button
   - Download as PDF button
4. Top banner ad slot
5. Shortcut sections rendered in required category order
6. Mobile inline ad inserted between section 3 (`Text`) and section 4 (`Video`) when viewport width matches mobile criteria
7. Bottom banner ad slot
8. Small footer noting source and print/share intent

Each section should render as:

- Sticky category sub-header
- Responsive two-column card grid on desktop
- Single-column stack on mobile
- Rows inside cards with action text left and key pills right

## Visual Direction

Visual direction is a blend:

- Functional/UI requirements from the written prompt are mandatory
- Figma should guide spacing, composition, hierarchy, and finish where compatible

Base design decisions:

- Screen theme defaults to dark
- Core palette starts from prompt values:
  - Page background `#111`
  - Card background `#1c1c1c`
  - Key pill background `#2a2a2a`
  - Text `#fff`
  - Accent `#a78bfa`
- All theme values defined via CSS custom properties on `:root`
- Typography loaded via Google Fonts import for Inter, per prompt
- Key pills styled like tactile keys: monospace text, border, inset/highlight feel, subtle shadow
- Hover states should be restrained: slight scale-up on pills, visible copy icon reveal on row hover

Figma integration rule:

- During implementation, inspect exact Figma node context and screenshot before final styling decisions
- If Figma conflicts with mandatory prompt features, preserve function first and use Figma as aesthetic guidance second

## Responsive Behavior

Desktop:

- Two-column layout for section cards
- Wide top banner ad centered with `728x90` placeholder sizing
- Top controls can sit in one row or wrap gracefully if needed

Mobile:

- Single-column card layout
- Touch-friendly control sizing
- Mobile inline ad rendered only on mobile-width viewports
- Copy affordance must remain available without relying solely on hover

Breakpoint behavior should be simple and explicit, with one main mobile threshold used consistently for layout and ad injection.

## Data Model

All shortcut data lives in a constant array near the bottom script:

```js
const SHORTCUTS = [
  {
    action: "Undo",
    mac: "Cmd + Z",
    windows: "Ctrl + Z",
    category: "Basic",
    tags: ["history", "edit", "undo"]
  }
];
```

Normalization rules:

- `category` must exactly map to one of the eight required section names
- `tags` should support search synonyms and likely user intent
- `mac` and `windows` remain human-readable strings and are tokenized into pill UI during render if needed
- Empty platform values are allowed only if the Canva source truly omits one, but implementation should avoid this if source provides a pair

## Rendering Strategy

Recommended implementation approach is a semantic single-file app with small DOM utility functions.

Suggested responsibilities inside `index.html`:

- HTML skeleton for shell, controls, ad slots, and root render containers
- CSS for theme, layout, pills, hover states, print overrides, and search highlight styling
- JS for:
  - dataset definition
  - platform detection
  - section grouping
  - initial render
  - search filtering and highlight
  - OS emphasis state
  - clipboard actions
  - mobile ad insertion
  - structured data injection if easier to serialize in JS than static HTML

Rendering should be data-driven from `SHORTCUTS`, not hand-authored row markup.

## Interaction Design

### Search

- Search filters all sections in real time as user types
- Match against action, category, platform strings, and tags
- Matching text in visible results should be highlighted
- `Escape` clears the input and restores full dataset
- Empty-state messaging should appear if no shortcuts match

### OS Toggle

- Detect default platform from `navigator.platform`
- Fallback default is Mac
- Toggle changes visual emphasis only; both Mac and Windows shortcuts remain visible
- Non-selected platform dims to about 40% opacity

### Copy Row

- Each row includes copy affordance
- On desktop it may reveal on hover; on touch/mobile it must still be directly tappable
- Copied format should be plain text, for example:
  `Undo — Mac: Cmd + Z | Windows: Ctrl + Z`

### Share

- Share button copies current page URL to clipboard
- Should work without external APIs beyond browser clipboard support
- If clipboard API fails, design should degrade gracefully, for example by selecting a fallback text field or surfacing a basic failure message

### Download as PDF

- Button triggers `window.print()`
- Print stylesheet must make output useful as a desk reference rather than a screenshot of the app shell

## Ad Slots

Required placeholders:

- `#ad-top-banner` with class `ad-slot` and `data-size="728x90"`
- `#ad-bottom-banner` with class `ad-slot` and `data-size="728x90"`
- `#ad-mobile-inline` with class `ad-slot` and `data-size="300x250"`

Rules:

- Top banner appears between controls and first section
- Bottom banner appears above footer
- Mobile inline slot is inserted between sections 3 and 4 only on mobile widths
- Ad slots are placeholders only, visually obvious and layout-stable

## SEO and Metadata

Required title:

`Canva Keyboard Shortcuts — Complete Cheat Sheet (Mac & Windows)`

Brand name used in visible UI:

`Canva Vault`

Metadata should include:

- Standard meta description
- Open Graph title, description, type, URL
- Twitter card tags
- Canonical-ready structure where possible, even if local file opening limits real URL use

Because user chose deployed-page-first priority:

- Metadata should be written for hosted use, accepting that local `file://` preview limits how share scrapers behave
- Use absolute-URL placeholders only if clearly identifiable for later replacement; otherwise prefer conservative tags that do not lie

## Structured Data

Include JSON-LD using `HowTo` schema wrapping the shortcut list.

Practical approach:

- Represent the page as a reference/how-to resource for using Canva shortcuts efficiently
- Use steps or sections that correspond to shortcut categories
- Avoid overclaiming unsupported fields

## Print Experience

Print mode should:

- Remove search bar, buttons, toggle chrome, footer clutter, and ad slots
- Switch to white background with black text
- Preserve legibility of key combinations
- Use a dense but readable two-column cheat-sheet layout
- Avoid clipped rows and sticky behavior artifacts

Print output is a core deliverable, not an afterthought.

## Accessibility

Even in a single-file static page, implementation should preserve:

- Semantic headings in correct order
- Button elements for clickable controls
- Sufficient contrast for dark mode and print mode
- Focus-visible treatment for keyboard users
- Search label or accessible name
- Copy/share controls with accessible text, not icon-only ambiguity

## Error Handling and Edge Cases

Implementation should explicitly handle:

- No search matches
- Clipboard API unavailable or denied
- Platform detection returning unknown value
- Categories with uneven item counts
- Mobile ad insertion only once, even on resize handling if resize support is implemented
- Shortcut strings that contain multi-key sequences or slash-separated alternatives

## File Plan

Expected files after implementation:

- Create `index.html`
- Create or retain `Prompt.txt` as source requirements note
- Create this design spec under `docs/superpowers/specs/`

No additional source files should be required.

## Testing and Verification Plan

Minimum manual verification after implementation:

- Open `index.html` directly from filesystem and confirm full render with no console errors
- Verify all eight categories render in correct order
- Verify strict-source shortcut count and spot-check entries against Canva help page
- Test live search, highlight, and `Escape` clear
- Test OS auto-detect and manual toggle
- Test row copy action output
- Test share button output
- Test print preview for clean two-column sheet
- Test mobile-width layout and inline ad placement
- Compare final visual output against Figma screenshot and adjust spacing/finish for closer parity where prompt constraints allow

## Open Decisions Resolved

- Prompt/Figma conflicts: blend, with prompt-required features preserved
- Shortcut source scope: strict Canva help page only
- Priority between local-open and deploy concerns: optimize for deployed page first while still keeping local file functionality

## Risks

- Canva help page may not provide 80 unique shortcuts while prompt requests 80+
- OG/Twitter metadata cannot be fully validated from local file usage
- Figma details may suggest layout changes that conflict with mandatory single-file prompt constraints

## Acceptance Criteria

The work is complete when:

- `index.html` exists as a self-contained single-file site
- Opening it directly in browser works without setup
- Shortcut data comes only from the Canva help page
- Required sections, controls, ad slots, metadata, JSON-LD, and print behavior are present
- Layout matches prompt requirements and is visually tuned using Figma where compatible
- No framework/build tooling is introduced
