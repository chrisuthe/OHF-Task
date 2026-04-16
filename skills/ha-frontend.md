# Home Assistant Frontend

**Repo:** `home-assistant/frontend` | **Base branch:** `dev` | **TypeScript + Lit 3.x**

---

## Research Hints

- Read `CLAUDE.md` and `AGENTS.md` first — they have extensive guidelines.
- This is a Lit 3.x Web Components project with TypeScript.
- For translation work, explain that English base strings live in `src/translations/en.json` and other languages are managed via Lokalise (never edit non-English translation files directly).

---

## Coding Standards

**Framework & tooling:**
- TypeScript strict + Lit 3.x Web Components
- ESLint (Airbnb + TypeScript strict + Lit + a11y) + Prettier
- Vitest for testing
- Has `CLAUDE.md` and `AGENTS.md` — **always read these first**

**Code style:**
- Never pass file args to `yarn lint` or `yarn lint:types` — run them without arguments
- Sentence case for ALL UI text, American English, Oxford comma
- "Home Assistant" — never "HA" or "HASS"
- "integration" — never "component" in user-facing text
- All user-facing strings must use `this.hass.localize("ui.panel.config.key", { param: value })`
- Never hardcode English strings in components

**Translations / i18n:**
- English base strings: `src/translations/en.json` (~556KB, JSON, dot-notated keys)
- Other languages managed via **Lokalise** — never edit non-English translation files directly
- Upload base: `script/translations_upload` (Docker + Lokalise CLI)
- Download: `script/translations_download` (gulp task)
- Dev setup: `script/setup_translations` runs `gulp setup-and-fetch-nightly-translations`

---

## Lint & Test

```bash
yarn lint              # ESLint (no file args!)
yarn format            # Prettier
yarn lint:types        # TypeScript type checking (no file args!)
yarn test              # Vitest
```

---

## PR Requirements

Target: `dev`

- Description and rationale
- Link to related issue (`Closes #<number>`)
- Confirm: lint and type checks pass
- For user-facing changes: documentation PR on `home-assistant.io`
- For translation changes: only modify `src/translations/en.json`
