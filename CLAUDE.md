# CLAUDE.md

Operating rules for Claude Code — and any maintainer — working in this repository.

## What this is

A **zero-dependency** Firefox/Chrome WebExtension (Manifest V3) that restyles and
improves the Claude web UI (`claude.ai`). It runs **entirely client-side**. See
`README.md` for the product vision and `LICENSE` for usage terms (source-available,
view-only).

## Prime directive: user safety and privacy come first

This extension runs on pages that contain users' **private, often sensitive
conversations**. Protecting that data outranks every feature. When a feature and
user privacy conflict, **privacy wins** — drop or redesign the feature.

Every architecture or code decision must pass the **Privacy Review loop** (bottom
of this file) *before* it lands. Reviewing through the lens of user data privacy
is not a final step — it's how we make each decision.

## Hard rules — never violate

### Data & network

- **No network. At all.** Zero outbound requests — no `fetch`, `XMLHttpRequest`,
  `WebSocket`, `navigator.sendBeacon`, and no `<img>`/CSS/font/favicon pings to any
  endpoint. There is no server. **User data never leaves the browser.**
- **No telemetry, analytics, tracking, A/B, or crash/error reporting** of any kind.
- **Never read, store, log, or transmit conversation content.** Read the DOM only
  as much as a visual tweak strictly requires; never persist or send it anywhere.
- **Store preferences only.** Persist nothing but the user's own UI settings/toggles,
  and prefer `storage.local` (stays on device). Never put anything sensitive in
  `storage.sync` (it syncs to the browser vendor's cloud).
- **No remote code, ever.** No `eval`, `new Function`, `setTimeout("string")`, no
  remote/CDN scripts, no dynamic `import()` of remote URLs. All code ships inside
  the package (also required by MV3).

### Permissions

- **Least privilege.** Request the narrowest permissions that work. Host permissions
  limited to Claude's domain(s) only — **never `<all_urls>`**.
- Do **not** request `tabs`, `cookies`, `webRequest`, `clipboardRead`, `history`,
  `downloads`, cross-site scripting, etc., unless a feature genuinely needs it — and
  then justify and document it (in the commit/PR and README).
- Keep a strict `content_security_policy` in the manifest.
- **Never touch auth.** Do not read or modify cookies, tokens, auth headers, or
  `localStorage`/`sessionStorage` used for sessions. Never weaken claude.ai's own security.

### DOM & code safety

- **No HTML injection from dynamic strings.** Never assign page- or user-derived
  data to `innerHTML` / `outerHTML` / `insertAdjacentHTML` / `document.write`. Build
  nodes with `createElement` + `textContent` and safe attribute APIs.
- **Isolated world only.** Keep logic in the content-script isolated world. Don't
  inject into the page's main/JS world unless strictly necessary and privacy-reviewed.
- **No broad input capture.** No global keystroke / input / paste / clipboard
  listeners (keylogger risk — and appearance of one). Scope every listener to the
  smallest element and event needed.
- **Fail closed.** If the DOM isn't what you expect, do nothing — never break, block,
  or hang claude.ai. Wrap feature initialization defensively.
- **Stay quiet.** No logging of user content; guard any debug output behind an
  off-by-default dev flag.

### Public-repo safety

- **No secrets, ever** — keys, tokens, credentials, `.env`. There's no backend, so
  there's nothing to commit; keep it that way.
- **No real user data** in code, tests, fixtures, screenshots, or commit messages.
  Redact conversations from any screenshot and use obviously fake sample data.
- **No personal PII** in committed files (the `LICENSE` deliberately points to the
  GitHub profile, not a personal email).
- **Stay auditable and honest.** Shipped behavior must match what the code and README
  say — no hidden behavior, no obfuscation or minification that defeats review. Anyone
  reading this public repo should be able to verify, line by line, that it is safe.

## Engineering constraints (locked at project setup)

- **Zero npm dependencies** — runtime *and* build. Only Node.js built-ins (`fs`,
  `path`, …) in the build script. No webpack / vite / rollup / esbuild.
- **Vanilla JS only** — no frameworks or libraries (React, Vue, jQuery, Tailwind, …).
- **ES modules + JSDoc** for type hints. No TypeScript toolchain.
- **Cross-browser** — Chrome & Firefox via WebExtensions (MV3); the build emits a
  per-browser bundle.
- If a feature seems to need a dependency, **make the feature smaller** — don't add
  the dependency.

## Code style

- Match the surrounding code. Small, focused, readable ES modules.
- Keep every visible feature behind a user toggle; default to the least-surprising behavior.
- Prefer **CSS over JS** for styling — less intrusive and easier to audit.
- Comment the *why*, especially for any DOM assumption tied to claude.ai's markup.

## Privacy Review loop — run on every change

Before writing or merging any feature or architecture change, answer all of these.
If any answer is uncomfortable, redesign.

1. **Does it touch user content?** If yes, what's the minimum needed — and is none of it persisted or sent?
2. **Does it store anything?** Exactly what, where (`storage.local`?), and why? (Preferences only.)
3. **Does it make any network request?** The answer must be **no**.
4. **Does it need a new permission?** Can a smaller scope work? Is it justified and documented?
5. **Could it leak data** — to the page, another extension, logs, the clipboard, or sync storage?
6. **Could it be abused** by someone reading this public repo, or harm a user if misused?
7. **Is it transparent and auditable** — does behavior match the docs, with no hidden actions?

> When in doubt, choose the option that collects, stores, and sends **less**.
