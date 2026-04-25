# Canva Vault

A single-file, zero-dependency, searchable, and printable reference site for Canva keyboard shortcuts. Built for speed, offline use, and high utility.

## Features

- **Search Everything:** Real-time filtering by action, category, keys, or tags.
- **Platform Awareness:** Auto-detects Mac/Windows and highlights the relevant shortcuts.
- **Copy to Clipboard:** One-tap copying of shortcut descriptions.
- **Print-Optimized:** A dedicated print stylesheet transforms the page into a clean, two-column PDF/paper cheat sheet.
- **Zero Build Step:** Pure HTML/CSS/JS. Works by opening `index.html` directly or deploying to any static host.
- **Offline Ready:** No external runtime dependencies (except Google Fonts).

## Project Structure

- `index.html`: The entire application (HTML, CSS, JS, and Data).
- `docs/superpowers/`: Design specifications and implementation plans.
- `netlify.toml`: Configuration for Netlify deployment.
- `og-preview.svg`: Open Graph preview image.
- `favicon.png`: Project icon.

## Development

Since this is a single-file application, development is straightforward:

1. Open `index.html` in your browser.
2. Edit `index.html` in your favorite editor.
3. Refresh the browser to see changes.

## Data Model

Shortcut data is stored as a constant array within `index.html`:

```javascript
const SHORTCUTS = [
  {
    action: "Undo",
    mac: "Cmd + Z",
    windows: "Ctrl + Z",
    category: "Basic",
    tags: ["history", "edit", "undo"]
  },
  // ...
];
```

## Deployment

The project is configured for one-click deployment to Netlify or any static hosting service. Simply upload the `index.html` and assets.

## Source of Truth

Shortcut content is strictly sourced from the official [Canva Help Center](https://www.canva.com/help/canva-keyboard-shortcuts/).
