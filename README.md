# Claude UI — Try Not To Cry 😢→🙂

> A **zero-dependency** browser extension that makes the [Claude](https://claude.ai) web UI cleaner, prettier, and more pleasant to use.

**Status:** 🚧 Early scaffolding — this README describes the project's vision and design. Code is on the way.

> ⚠️ **Unofficial.** This is an independent, community project. It is **not** affiliated with, endorsed by, or supported by Anthropic. It only restyles and enhances the Claude web interface in your own browser.

---

## Why?

The Claude web UI is great, but everyone has that one thing they wish were
different — the chat is too narrow, the code blocks need more contrast, you
want a true-black OLED theme, bigger fonts, less clutter. This extension layers
those quality-of-life tweaks on top of Claude, **entirely on your machine**,
without touching Anthropic's servers or your data.

## Principles (the non-negotiables)

These constraints are the whole point of the project. Every change must respect them:

- 🚫 **Zero npm dependencies** — runtime *and* build. The only tool required is Node.js itself; the build script uses **only Node's built-in modules** (`fs`, `path`, …).
- 🍦 **Vanilla JS only** — no frameworks. No React, Vue, Svelte, jQuery, Tailwind, etc.
- 📦 **ES modules + [JSDoc](https://jsdoc.app/) types** — type hints in your editor without a TypeScript toolchain.
- 🛠️ **Custom build script** — no webpack / vite / rollup / esbuild. We own the (tiny) builder.
- 🔒 **Client-side only** — no network calls, no analytics, no tracking. Nothing leaves your browser.
- 🦊 **+** 🌐 **Cross-browser** — Firefox & Chrome, via the [WebExtensions API](https://developer.mozilla.org/docs/Mozilla/Add-ons/WebExtensions) (Manifest V3).

If a feature seems to need a dependency, that's a signal to write a smaller feature — not to add the dependency.

## Planned features

Nothing is built yet. This is the wishlist we're aiming at (contributions welcome):

- [ ] 🎨 **Themes** — true-black OLED dark mode, light variants, custom accent color
- [ ] 🔤 **Typography** — font family, size, and line-height controls for readability
- [ ] ↔️ **Layout** — adjustable / full-width chat column, tweakable message spacing
- [ ] 💻 **Better code blocks** — higher contrast, soft-wrap toggle, language label
- [ ] 🧘 **Distraction-free mode** — hide sidebar / clutter for focused reading
- [ ] ⌨️ **Keyboard shortcuts** — quick actions without reaching for the mouse
- [ ] 💾 **Per-site settings** — preferences persisted with `storage`, synced across pages

> All toggles live in the popup / options page so you can enable only what you want.

## Browser support

| Browser | Supported | Notes |
| ------- | --------- | ----- |
| Chrome / Chromium / Edge | ✅ | Manifest V3, `service_worker` background |
| Firefox | ✅ | Manifest V3, `scripts` background + `browser_specific_settings` |

The build emits a separate, ready-to-load bundle per browser to handle the small
manifest differences between Chrome and Firefox.

## Project structure

> Proposed layout — created as the project grows.

```text
claude-ui-try-not-to-cry/
├── src/
│   ├── manifest.json        # Base WebExtension manifest (MV3)
│   ├── content/             # Content scripts injected into claude.ai
│   │   ├── index.js
│   │   └── styles.css
│   ├── background/          # Background service worker
│   │   └── service-worker.js
│   ├── popup/               # Toolbar popup UI (enable/disable features)
│   ├── options/             # Full settings page
│   ├── lib/                 # Shared vanilla-JS ES modules
│   └── assets/icons/        # Icons & images
├── build/
│   └── build.mjs            # Custom Node build script (built-ins only)
├── dist/                    # Build output (git-ignored)
│   ├── chrome/              # → load this in Chrome
│   └── firefox/             # → load this in Firefox
└── README.md
```

## Getting started

### Prerequisites

- **Node.js** (any recent LTS) — used *only* to run the build script. No `npm install` step; there is nothing to install.

### Build

```sh
git clone https://github.com/ihorml/claude-ui-try-not-to-cry.git
cd claude-ui-try-not-to-cry
node build/build.mjs        # outputs dist/chrome and dist/firefox
```

### Load the extension (development)

**Chrome / Edge**

1. Open `chrome://extensions`
2. Enable **Developer mode** (top-right)
3. Click **Load unpacked** and select the `dist/chrome` folder

**Firefox**

1. Open `about:debugging#/runtime/this-firefox`
2. Click **Load Temporary Add-on…**
3. Select `dist/firefox/manifest.json`

> Firefox temporary add-ons are removed on restart. Permanent installation
> requires signing through [AMO](https://addons.mozilla.org), which we'll set up
> for releases later.

## How the build works

The builder is a small, readable Node script — no magic:

1. Copies `src/` into per-browser `dist/` folders.
2. Generates the correct `manifest.json` for each target (e.g. Chrome's
   `service_worker` vs. Firefox's `scripts`, plus `browser_specific_settings`).
3. Concatenates source ES modules into a single classic script for content
   scripts, which don't support native ES modules.

That's the entire reason a custom builder earns its keep — and why we don't need a bundler.

## Contributing

The bar for contributions is simple: **keep it dependency-free and vanilla.**

- No npm packages (runtime or dev). If you reach for one, rethink the approach.
- Plain ES modules; annotate types with JSDoc.
- Match the surrounding code's style and keep features behind toggles.

## License

TBD — likely MIT. A `LICENSE` file will be added before the first release.
