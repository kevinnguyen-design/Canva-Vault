# Canva Vault Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build `index.html` as a single-file Canva shortcut reference site branded as Canva Vault, with strict Canva-source shortcut data, searchable UI, OS emphasis toggle, copy/share/print actions, ad placeholders, deploy-ready metadata, and Figma-guided polish.

**Architecture:** Keep everything in one semantic `index.html` file with three layers: HTML shell, CSS theme/layout/print rules, and bottom-of-body vanilla JS for data, rendering, interaction state, and metadata serialization. Use one strict `SHORTCUTS` array as source of truth and generate all category sections from that array in required order.

**Tech Stack:** Plain HTML, CSS custom properties, Google Fonts import, vanilla JavaScript, browser Clipboard API, `window.print()`

---

## File Structure

- Create: `index.html`
- Modify: `Prompt.txt` only if you want to preserve a final note, otherwise leave unchanged
- Reference: `docs/superpowers/specs/2026-04-24-canva-shortcuts-site-design.md`
- Output plan: `docs/superpowers/plans/2026-04-24-canva-vault.md`

Notes for workers:

- Workspace is not a git repo right now. Do not add commit steps unless the user initializes git first.
- Shortcut data must come from Canva help page only.
- Figma implementation step requires design-context + screenshot fetch before final visual tuning.

### Task 1: Scaffold `index.html` shell and base styling

**Files:**
- Create: `index.html`
- Reference: `docs/superpowers/specs/2026-04-24-canva-shortcuts-site-design.md`

- [ ] **Step 1: Create the document shell with required head metadata and branded header**

Use this as the starting structure:

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Canva Keyboard Shortcuts — Complete Cheat Sheet (Mac &amp; Windows)</title>
    <meta
      name="description"
      content="Canva Vault is a complete Canva keyboard shortcuts cheat sheet for Mac and Windows with search, copy, share, and print-friendly output."
    />
    <meta property="og:title" content="Canva Keyboard Shortcuts — Complete Cheat Sheet (Mac &amp; Windows)" />
    <meta
      property="og:description"
      content="Canva Vault collects Canva keyboard shortcuts into a searchable, printable reference for Mac and Windows."
    />
    <meta property="og:type" content="website" />
    <meta property="og:url" content="" />
    <meta name="twitter:card" content="summary_large_image" />
    <meta name="twitter:title" content="Canva Keyboard Shortcuts — Complete Cheat Sheet (Mac &amp; Windows)" />
    <meta
      name="twitter:description"
      content="Canva Vault collects Canva keyboard shortcuts into a searchable, printable reference for Mac and Windows."
    />
    <style>
      :root {
        --bg: #111111;
        --surface: #1c1c1c;
        --surface-2: #232323;
        --key-bg: #2a2a2a;
        --key-border: rgba(255, 255, 255, 0.18);
        --text: #ffffff;
        --muted: rgba(255, 255, 255, 0.72);
        --dimmed: 0.4;
        --accent: #a78bfa;
        --line: rgba(255, 255, 255, 0.08);
        --shadow: 0 10px 30px rgba(0, 0, 0, 0.28);
        --radius: 20px;
        --mobile-breakpoint: 860px;
      }
    </style>
  </head>
  <body>
    <div class="page-shell">
      <header class="hero">
        <p class="eyebrow">Canva shortcut reference</p>
        <div class="hero-topline">
          <h1>Canva Vault</h1>
          <p class="hero-summary">Complete Canva keyboard shortcuts for Mac and Windows in one searchable, printable reference.</p>
        </div>
      </header>
      <section class="top-controls" aria-label="Shortcut controls"></section>
      <div id="ad-top-banner" class="ad-slot" data-size="728x90"></div>
      <main id="shortcut-sections"></main>
      <div id="ad-bottom-banner" class="ad-slot" data-size="728x90"></div>
      <footer class="site-footer"></footer>
    </div>
    <script>
    </script>
  </body>
</html>
```

- [ ] **Step 2: Add the visible top controls markup and empty-state container**

Insert this inside `.top-controls` and below `#shortcut-sections`:

```html
<div class="controls-bar">
  <label class="search-field">
    <span class="sr-only">Search shortcuts</span>
    <input id="search-input" type="search" placeholder="Search shortcuts, keys, or tags" autocomplete="off" />
  </label>
  <div class="controls-actions">
    <div class="os-toggle" role="group" aria-label="Preferred operating system">
      <button type="button" class="os-button" data-os="mac" aria-pressed="false">Mac</button>
      <button type="button" class="os-button" data-os="windows" aria-pressed="false">Windows</button>
    </div>
    <button type="button" id="share-page">Share this page</button>
    <button type="button" id="print-page">Download as PDF</button>
  </div>
</div>
<p id="search-status" class="search-status" aria-live="polite"></p>
```

```html
<div id="empty-state" class="empty-state" hidden>
  <h2>No matches</h2>
  <p>Try a different action, key combo, or tag.</p>
</div>
```

- [ ] **Step 3: Add base CSS for layout, cards, controls, sticky headers, and ad slots**

Add at minimum these rule groups inside `<style>`:

```css
@import url("https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap");

* { box-sizing: border-box; }
html { color-scheme: dark; }
body {
  margin: 0;
  min-width: 320px;
  font-family: "Inter", sans-serif;
  background:
    radial-gradient(circle at top, rgba(167, 139, 250, 0.22), transparent 34rem),
    linear-gradient(180deg, #151515 0%, var(--bg) 48%);
  color: var(--text);
}
.page-shell { width: min(1200px, calc(100% - 32px)); margin: 0 auto; padding: 32px 0 64px; }
.hero,
.top-controls,
.ad-slot,
.section-card,
.empty-state { border: 1px solid var(--line); background: rgba(28, 28, 28, 0.88); box-shadow: var(--shadow); backdrop-filter: blur(14px); }
.hero,
.top-controls,
.empty-state,
.site-footer { border-radius: var(--radius); }
.controls-bar { display: flex; gap: 16px; justify-content: space-between; align-items: center; flex-wrap: wrap; }
.search-field { flex: 1 1 320px; }
.search-field input { width: 100%; }
.controls-actions { display: flex; gap: 12px; flex-wrap: wrap; align-items: center; }
.section-block { margin-top: 28px; }
.section-heading {
  position: sticky;
  top: 12px;
  z-index: 5;
  margin: 0 0 14px;
  padding: 12px 16px;
  border: 1px solid var(--line);
  border-radius: 999px;
  background: rgba(17, 17, 17, 0.86);
  backdrop-filter: blur(10px);
}
.section-grid { display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap: 16px; }
.section-card { border-radius: 18px; overflow: hidden; }
.shortcut-row { display: grid; grid-template-columns: minmax(0, 1fr) auto; gap: 16px; align-items: center; padding: 14px 16px; border-top: 1px solid var(--line); }
.shortcut-row:first-child { border-top: 0; }
.shortcut-keys { display: flex; gap: 8px; align-items: center; flex-wrap: wrap; }
.key-pill {
  display: inline-flex;
  align-items: center;
  min-height: 34px;
  padding: 0 10px;
  border-radius: 10px;
  border: 1px solid var(--key-border);
  background: var(--key-bg);
  font: 600 0.86rem/1.2 ui-monospace, SFMono-Regular, Menlo, Consolas, monospace;
  letter-spacing: 0.02em;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.08), 0 2px 4px rgba(0, 0, 0, 0.22);
  transition: transform 160ms ease, opacity 160ms ease;
}
.key-pill:hover { transform: scale(1.04); }
.shortcut-copy { opacity: 0; pointer-events: none; }
.shortcut-row:hover .shortcut-copy,
.shortcut-row:focus-within .shortcut-copy { opacity: 1; pointer-events: auto; }
.ad-slot {
  display: grid;
  place-items: center;
  min-height: 90px;
  margin: 20px 0;
  color: var(--muted);
  text-transform: uppercase;
  letter-spacing: 0.18em;
}
@media (max-width: 860px) {
  .page-shell { width: min(100% - 24px, 1200px); padding-top: 20px; }
  .section-grid { grid-template-columns: 1fr; }
  .shortcut-copy { opacity: 1; pointer-events: auto; }
}
```

- [ ] **Step 4: Open the page locally and verify shell renders before adding data logic**

Run:

```bash
rtk xdg-open index.html
```

Expected:

- Browser opens file directly
- Header, controls area, top and bottom ad placeholders, and empty main region appear
- No JS errors from missing DOM hooks yet

### Task 2: Build strict shortcut dataset and rendering pipeline

**Files:**
- Modify: `index.html`
- Reference: `https://www.canva.com/help/canva-keyboard-shortcuts/`

- [ ] **Step 1: Collect and normalize the shortcut list from Canva help page**

Create a working dataset shape inside the script block:

```js
const CATEGORY_ORDER = [
  "Basic",
  "Elements",
  "Text",
  "Video",
  "Zoom",
  "Page view",
  "Comments",
  "Desktop app"
];

const SHORTCUTS = [
  {
    action: "Undo",
    mac: "Cmd + Z",
    windows: "Ctrl + Z",
    category: "Basic",
    tags: ["undo", "history", "edit"]
  }
  // Add every Canva-published shortcut here, grouped by required categories only.
];
```

Normalization rules to apply while collecting:

```js
function normalizeShortcut(raw) {
  return {
    action: raw.action.trim(),
    mac: raw.mac.trim(),
    windows: raw.windows.trim(),
    category: raw.category,
    tags: raw.tags.map((tag) => tag.toLowerCase())
  };
}
```

Expected outcome:

- Final array contains only Canva-published shortcuts
- Every record has all five required fields
- Category strings exactly match `CATEGORY_ORDER`

- [ ] **Step 2: Add render helpers for sections, rows, key pills, and highlight-safe text**

Add these utilities:

```js
const state = {
  query: "",
  preferredOS: "mac",
  mobileAdInserted: false
};

function escapeHTML(value) {
  return value
    .replaceAll("&", "&amp;")
    .replaceAll("<", "&lt;")
    .replaceAll(">", "&gt;")
    .replaceAll('"', "&quot;");
}

function highlightMatch(value, query) {
  const safe = escapeHTML(value);
  if (!query) return safe;
  const pattern = new RegExp(`(${query.replace(/[.*+?^${}()|[\\]\\\\]/g, "\\$&")})`, "ig");
  return safe.replace(pattern, "<mark>$1</mark>");
}

function renderKeyGroup(label, combo, active) {
  const keys = combo.split(" + ").map((part) => `<span class="key-pill">${escapeHTML(part)}</span>`).join("");
  return `<div class="platform-group ${active ? "is-active" : "is-dimmed"}" aria-label="${label}">${keys}</div>`;
}
```

- [ ] **Step 3: Implement search filtering and grouped rendering**

Add these functions:

```js
function matchesShortcut(shortcut, query) {
  if (!query) return true;
  const haystack = [
    shortcut.action,
    shortcut.mac,
    shortcut.windows,
    shortcut.category,
    ...shortcut.tags
  ].join(" ").toLowerCase();
  return haystack.includes(query.toLowerCase());
}

function groupShortcuts(shortcuts) {
  return CATEGORY_ORDER.map((category) => ({
    category,
    items: shortcuts.filter((shortcut) => shortcut.category === category)
  }));
}

function renderSections() {
  const root = document.querySelector("#shortcut-sections");
  const emptyState = document.querySelector("#empty-state");
  const filtered = SHORTCUTS.filter((shortcut) => matchesShortcut(shortcut, state.query));
  const groups = groupShortcuts(filtered);
  const visibleGroups = groups.filter((group) => group.items.length > 0);

  if (!visibleGroups.length) {
    root.innerHTML = "";
    emptyState.hidden = false;
    return;
  }

  emptyState.hidden = true;
  root.innerHTML = visibleGroups.map((group, index) => {
    const cards = [];
    for (let i = 0; i < group.items.length; i += 2) {
      const slice = group.items.slice(i, i + 2);
      cards.push(`
        <article class="section-card">
          ${slice.map((shortcut) => renderShortcutRow(shortcut)).join("")}
        </article>
      `);
    }

    return `
      <section class="section-block" data-category="${group.category}">
        <h2 class="section-heading">${group.category}</h2>
        <div class="section-grid">${cards.join("")}</div>
      </section>
    `;
  }).join("");

  maybeInsertMobileAd();
  updateSearchStatus(filtered.length);
}
```

- [ ] **Step 4: Verify full strict-data render**

Run:

```bash
rtk xdg-open index.html
```

Expected:

- All required categories render in order when they have entries
- Each row shows action text and both platform key groups
- Rows come from `SHORTCUTS`, not hard-coded HTML

### Task 3: Add interactions for search, OS emphasis, copy, share, print, and mobile ad insertion

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add OS detection and toggle state wiring**

Insert these functions:

```js
function detectPreferredOS() {
  const platform = (navigator.platform || "").toLowerCase();
  return /mac|iphone|ipad|ipod/.test(platform) ? "mac" : "windows";
}

function updateOSToggle() {
  document.querySelectorAll(".os-button").forEach((button) => {
    const active = button.dataset.os === state.preferredOS;
    button.setAttribute("aria-pressed", String(active));
    button.classList.toggle("is-active", active);
  });
}

function applyOSClasses() {
  document.documentElement.dataset.preferredOs = state.preferredOS;
}
```

Use these on startup:

```js
state.preferredOS = detectPreferredOS() || "mac";
updateOSToggle();
applyOSClasses();
```

- [ ] **Step 2: Make row rendering include copy button and OS-emphasis classes**

Use this row renderer:

```js
function renderShortcutRow(shortcut) {
  const macActive = state.preferredOS === "mac";
  const windowsActive = state.preferredOS === "windows";
  return `
    <div class="shortcut-row">
      <div class="shortcut-copy-wrap">
        <p class="shortcut-action">${highlightMatch(shortcut.action, state.query)}</p>
        <button
          type="button"
          class="shortcut-copy"
          data-copy="${escapeHTML(`${shortcut.action} — Mac: ${shortcut.mac} | Windows: ${shortcut.windows}`)}"
          aria-label="Copy ${escapeHTML(shortcut.action)}"
        >
          Copy
        </button>
      </div>
      <div class="shortcut-keys">
        ${renderKeyGroup("Mac keys", shortcut.mac, macActive)}
        ${renderKeyGroup("Windows keys", shortcut.windows, windowsActive)}
      </div>
    </div>
  `;
}
```

Add CSS:

```css
[data-preferred-os="mac"] .platform-group.is-dimmed,
[data-preferred-os="windows"] .platform-group.is-dimmed {
  opacity: var(--dimmed);
}
.platform-group.is-active { opacity: 1; }
.shortcut-copy-wrap { display: flex; gap: 10px; align-items: center; min-width: 0; }
.shortcut-action { margin: 0; }
```

- [ ] **Step 3: Add event handlers for search, escape-clear, copy, share, and print**

Insert this logic:

```js
async function copyText(value) {
  if (navigator.clipboard && navigator.clipboard.writeText) {
    await navigator.clipboard.writeText(value);
    return true;
  }
  const input = document.createElement("textarea");
  input.value = value;
  document.body.appendChild(input);
  input.select();
  const ok = document.execCommand("copy");
  input.remove();
  return ok;
}

function updateSearchStatus(count) {
  const status = document.querySelector("#search-status");
  status.textContent = state.query
    ? `${count} shortcut${count === 1 ? "" : "s"} match “${state.query}”.`
    : `${SHORTCUTS.length} shortcuts loaded.`;
}

function attachEvents() {
  const searchInput = document.querySelector("#search-input");
  searchInput.addEventListener("input", (event) => {
    state.query = event.target.value.trim();
    renderSections();
  });
  searchInput.addEventListener("keydown", (event) => {
    if (event.key === "Escape") {
      event.preventDefault();
      searchInput.value = "";
      state.query = "";
      renderSections();
    }
  });

  document.querySelectorAll(".os-button").forEach((button) => {
    button.addEventListener("click", () => {
      state.preferredOS = button.dataset.os;
      updateOSToggle();
      applyOSClasses();
      renderSections();
    });
  });

  document.addEventListener("click", async (event) => {
    const copyButton = event.target.closest(".shortcut-copy");
    if (copyButton) {
      await copyText(copyButton.dataset.copy);
      return;
    }
  });

  document.querySelector("#share-page").addEventListener("click", async () => {
    await copyText(window.location.href);
  });

  document.querySelector("#print-page").addEventListener("click", () => {
    window.print();
  });
}
```

- [ ] **Step 4: Add mobile ad insertion helper**

Use:

```js
function isMobileWidth() {
  return window.innerWidth <= 860;
}

function maybeInsertMobileAd() {
  const existing = document.querySelector("#ad-mobile-inline");
  if (!isMobileWidth()) {
    if (existing) existing.remove();
    state.mobileAdInserted = false;
    return;
  }
  if (state.mobileAdInserted) return;

  const sections = document.querySelectorAll(".section-block");
  const anchor = sections[2];
  if (!anchor) return;

  const ad = document.createElement("div");
  ad.id = "ad-mobile-inline";
  ad.className = "ad-slot";
  ad.dataset.size = "300x250";
  ad.textContent = "Ad slot 300x250";
  anchor.insertAdjacentElement("afterend", ad);
  state.mobileAdInserted = true;
}

window.addEventListener("resize", () => {
  maybeInsertMobileAd();
});
```

- [ ] **Step 5: Initialize the app in correct order**

End script with:

```js
state.preferredOS = detectPreferredOS() || "mac";
updateOSToggle();
applyOSClasses();
renderSections();
attachEvents();
updateSearchStatus(SHORTCUTS.length);
```

- [ ] **Step 6: Verify behavior manually**

Run:

```bash
rtk xdg-open index.html
```

Expected:

- Search filters in real time
- `Escape` clears search
- Mac/Windows toggle changes emphasis
- Copy button copies row text
- Share button copies current URL
- Print button opens print dialog
- Mobile-width emulation shows inline ad between third and fourth visible sections

### Task 4: Add print polish, JSON-LD, and accessibility finishing

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add print stylesheet**

Append these rules:

```css
@media print {
  :root {
    --bg: #ffffff;
    --surface: #ffffff;
    --surface-2: #ffffff;
    --key-bg: #ffffff;
    --text: #000000;
    --line: rgba(0, 0, 0, 0.16);
  }

  body {
    background: #ffffff;
    color: #000000;
    font-size: 10.5pt;
  }

  .top-controls,
  .ad-slot,
  .site-footer,
  .shortcut-copy,
  .search-status,
  .eyebrow {
    display: none !important;
  }

  .page-shell { width: 100%; padding: 0; }
  .hero { border: 0; box-shadow: none; background: transparent; padding: 0 0 12pt; }
  .section-heading {
    position: static;
    border: 0;
    border-bottom: 1px solid var(--line);
    border-radius: 0;
    background: transparent;
    padding-left: 0;
  }
  #shortcut-sections { column-count: 2; column-gap: 18pt; }
  .section-block,
  .section-card { break-inside: avoid; box-shadow: none; background: transparent; }
  .section-grid { display: block; }
}
```

- [ ] **Step 2: Inject JSON-LD from shortcut data**

Add serializer:

```js
function buildStructuredData() {
  return {
    "@context": "https://schema.org",
    "@type": "HowTo",
    name: "Canva Keyboard Shortcuts — Complete Cheat Sheet (Mac & Windows)",
    description: "Canva Vault is a searchable and printable reference for Canva keyboard shortcuts on Mac and Windows.",
    step: CATEGORY_ORDER.map((category) => ({
      "@type": "HowToStep",
      name: category,
      text: SHORTCUTS
        .filter((shortcut) => shortcut.category === category)
        .map((shortcut) => `${shortcut.action}: Mac ${shortcut.mac}; Windows ${shortcut.windows}`)
        .join(" | ")
    }))
  };
}

function injectStructuredData() {
  const script = document.createElement("script");
  script.type = "application/ld+json";
  script.textContent = JSON.stringify(buildStructuredData());
  document.head.appendChild(script);
}
```

Call it during startup before final render:

```js
injectStructuredData();
```

- [ ] **Step 3: Finish accessibility details**

Confirm and add:

```html
<a class="skip-link" href="#shortcut-sections">Skip to shortcuts</a>
```

```css
.sr-only,
.skip-link:not(:focus) {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
.skip-link:focus,
button:focus-visible,
input:focus-visible {
  outline: 2px solid var(--accent);
  outline-offset: 2px;
}
```

Expected:

- Page is keyboard navigable
- Controls have labels
- Search status updates announce changes

### Task 5: Figma pass and final verification

**Files:**
- Modify: `index.html`
- Reference: supplied Figma URL

- [ ] **Step 1: Pull Figma design context for exact node and screenshot before visual tuning**

Do this before changing final visual details:

```text
1. Run Figma get_design_context on the approved node.
2. If response is too large, run get_metadata then re-fetch exact node.
3. Run get_screenshot for the same node.
4. Compare spacing, hierarchy, card shape, control density, and visual rhythm against current page.
```

Expected:

- You have both structured Figma context and screenshot before final styling adjustments

- [ ] **Step 2: Tune CSS to match Figma where compatible with prompt requirements**

Allowed tuning areas:

```text
- spacing scale
- hero hierarchy
- border radius
- surface layering
- card padding
- control styling
- accent usage
- typography weight/size
```

Do not break:

```text
- single-file architecture
- dark mode requirement
- required controls
- required categories
- ad slot placement
- print behavior
- both-platform visible shortcut rows
```

- [ ] **Step 3: Run final verification checklist**

Run:

```bash
rtk xdg-open index.html
```

Check all of these:

```text
- file opens directly with no setup
- title and visible brand are both correct
- strict Canva-source shortcut data only
- required sections present in order
- search, highlight, Escape clear work
- OS toggle works and dims inactive platform
- copy/share/print work
- ad slots render in correct locations
- print preview is readable and two-column
- no console errors
- Figma pass improved fidelity without violating prompt requirements
```

## Self-Review

Spec coverage:

- Single-file output covered in Tasks 1-4
- Strict Canva-source dataset covered in Task 2
- Search/highlight/escape covered in Task 3
- OS toggle, copy/share/print, ads covered in Task 3
- SEO metadata and JSON-LD covered in Tasks 1 and 4
- Print stylesheet covered in Task 4
- Figma parity pass covered in Task 5

Placeholder scan:

- No `TODO`, `TBD`, or “implement later” placeholders remain
- External data collection is explicit and limited to Canva help page

Type consistency:

- `SHORTCUTS`, `CATEGORY_ORDER`, and `state` names remain consistent across tasks
- Preferred OS uses `mac` and `windows` consistently

Plan complete and saved to `docs/superpowers/plans/2026-04-24-canva-vault.md`. Two execution options:

1. Subagent-Driven (recommended) - I dispatch a fresh subagent per task, review between tasks, fast iteration

2. Inline Execution - Execute tasks in this session using executing-plans, batch execution with checkpoints

Which approach?
