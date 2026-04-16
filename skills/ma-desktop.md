# Music Assistant Desktop App

**Repo:** `music-assistant/desktop-app` | **Base branch:** `main` | **Tauri v2 (Rust + WebView)**

---

## Research Hints

- Read `CONTRIBUTING.md` for architecture overview.
- Identify whether changes are in Rust (`src-tauri/`) or the WebView layer.
- Explain how the Tauri bridge connects Rust and the WebView.

---

## Coding Standards

**How it works:**
The desktop app discovers MA servers via mDNS, opens a WebView to the server's hosted frontend, and adds native features on top via Rust:
- SendSpin audio client (bit-perfect native playback via `cpal`)
- OS media controls (macOS Control Center, Windows Media Controls, Linux MPRIS) via `souvlaki`
- Discord Rich Presence
- mDNS server discovery
- System tray integration
- Auto-start, auto-update (Tauri updater plugin)

**Framework & tooling:**
- Tauri v2 (Rust backend + native WebView wrapping the MA web frontend)
- Package manager: **Yarn** (Husky + lint-staged auto-installed on `yarn install`)
- Minimum Rust version: 1.77.2

**Rust coding standards:**
- Clippy in **pedantic** mode — all pedantic warnings enabled with specific allows
- `unsafe_code` is **denied**
- `rustfmt` for formatting
- Key deps: `tauri` 2.9, `sendspin` (git dep from `sendspin/sendspin-rs`), `cpal`, `souvlaki`, `tokio`, `tokio-tungstenite`

**JS/HTML/JSON/MD standards:**
- Prettier for formatting
- Husky pre-commit hooks block commits if any check fails

---

## Lint & Test

```bash
yarn install       # Install deps + set up Husky hooks
yarn dev           # Tauri dev server (hot reload)
yarn build         # Production build
yarn lint          # All linters (clippy pedantic + prettier)
yarn lint:rust     # Clippy only
yarn lint:format   # Prettier only
yarn format        # Auto-format everything
yarn format:rust   # rustfmt only
```

---

## PR Requirements

Target: `main`

- Description and rationale
- Whether changes are Rust-side (`src-tauri/`) or WebView-side
- Link to related issue
- Confirm: `yarn lint` passes (clippy pedantic + prettier)
