# start-ohf-task

A [Claude Code](https://claude.com/claude-code) skill that helps contributors open well-scoped, standards-compliant pull requests against [Open Home Foundation](https://www.openhomefoundation.org/) projects — including Home Assistant, ESPHome, Music Assistant, OHF-Voice, and SendSpin.

When a contributor says "start an HA task" or "start an ESPHome task" to Claude, this skill:

1. Detects which OHF project they're in from the git remote.
2. Loads the coding standards, lint/test commands, and PR requirements specific to that project.
3. Creates an isolated git worktree on a properly-prefixed feature branch off the correct base (`dev` for HA/ESPHome, `main` for MA/OHF-Voice/SendSpin).
4. Walks the contributor through research, planning, implementation, and PR creation — explaining choices as it goes, not dumping code.

---

## Why this exists (for project maintainers)

If you maintain an OHF project, you've probably seen AI-assisted PRs that get the basics wrong: targeted at the wrong base branch, missing type hints, logging with periods, using `Any` where a specific type exists, skipping `hassfest`, ignoring config flow requirements, or landing as a sprawling single commit that's impossible to review.

This skill encodes the actual, project-specific rules you already document in `CLAUDE.md` / `AGENTS.md` / `CONTRIBUTING.md` and makes Claude follow them by default. Each sub-skill file captures a concrete slice of your standards:

| Project | Base branch | Enforced rules include |
|---|---|---|
| `home-assistant/core` | `dev` | Ruff, PEP 8, Google docstrings, strict typing where applicable, `hassfest`, Quality Scale references, logging rules (no component prefix, no trailing periods, `%s` for logs / f-strings elsewhere), max 5 open PRs |
| `home-assistant/frontend` | `dev` | Lit/TypeScript conventions, component patterns |
| `esphome/esphome` | `dev` | C++/Python dual-side rules, clang-format, component structure |
| `music-assistant/server` | `main` | Ruff, async patterns, provider structure, HAOS dev-addon test instructions in PRs |
| `music-assistant/frontend` | `main` | Vue/TS conventions, HAOS dev-addon test instructions |
| `music-assistant/mobile-app` | `main` | Kotlin Multiplatform + Compose Multiplatform (Android + iOS from shared codebase), Gradle Kotlin DSL, Koin DI, Material3, `StateFlow` over `LiveData`, no `!!` in live code, minimize `expect/actual` |
| `music-assistant/desktop-app` | `main` | Tauri v2 (Rust backend + WebView), Yarn, wraps the MA web frontend with native extras (SendSpin audio, OS media controls, Discord Rich Presence, mDNS discovery) |
| `OHF-Voice/*` | `main` | Voice pipeline / wake-word / intent conventions |
| `sendspin/*` | `main` | Conventional-commit prefixes (`feat/`, `fix/`, `refactor/`, `test/`, `chore/`) |

The `shared.md` sub-skill encodes universal OHF contribution principles: keep PRs small and focused, one logical change per PR, never amend after opening a PR (preserves review trail), feature branch per PR, test locally first, document user-facing changes, review is not personal.

### What you should see in a contributor's PR when they use this skill

- **Correct target branch** — `dev` for HA/ESPHome, `main` elsewhere.
- **Correct branch prefix** — `feat/`, `fix/` (auto-selected when the issue has a `bug` label), plus `refactor/`, `test/`, `chore/` on SendSpin.
- **One logical change per PR**, not a grab-bag.
- **No post-review amends** — the contributor was told up-front that rewriting history loses the review trail.
- **Project-specific lint/test commands run before push** — `ruff`, `mypy`, `pylint`, `hassfest`, `pytest`, project-specific equivalents.
- **PR body formatted per your project's requirements** — including the "Closes #N" link, breaking-change notes, and docs-PR links where applicable.
- **A contributor who can explain their own PR** — the skill's "Educational Approach" requires Claude to pause after each logical chunk, explain what it did and why, and ask the user to confirm understanding before moving on. The goal is that the contributor can answer your review comments from their own understanding of the code, not by re-prompting Claude.

### What this skill does NOT do

- It does not replace your `CONTRIBUTING.md`, CI, or review process. It's a front-loaded checklist, not a quality gate.
- It does not auto-merge or skip review. Every PR still goes through your normal review workflow.
- It does not bypass CODEOWNERS, label gates, or required checks.
- It does not stop a determined contributor from disabling it — but it makes the well-trodden path the path of least resistance.

### Suggesting improvements

If your project has rules that aren't captured, or rules that have changed, please open a PR against the relevant file in [`skills/`](./skills/):

- `skills/shared.md` — universal OHF principles
- `skills/ha-core.md`, `skills/ha-frontend.md`, `skills/esphome.md`, `skills/ma-server.md`, `skills/ma-frontend.md`, `skills/ma-mobile.md`, `skills/ma-desktop.md`, `skills/ohf-voice.md`, `skills/sendspin.md` — per-project rules

Each file is short, human-readable Markdown — no DSL, no codegen. If your project's `CLAUDE.md` / `AGENTS.md` already documents the rule, linking to the canonical doc from the sub-skill is preferred over duplicating it.

---

## How contributors install and use it

See [GETTING_STARTED.md](./GETTING_STARTED.md).

## License

MIT (see [LICENSE](./LICENSE) if present; otherwise treat as MIT unless stated otherwise).
