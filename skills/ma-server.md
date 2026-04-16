# Music Assistant Server

**Repo:** `music-assistant/server` | **Base branch:** `main` | **Python 3.12**

---

## Research Hints

- Check if the change affects a music provider module (which may have relaxed mypy rules).
- Explain the provider architecture and how providers plug into the server.

---

## Coding Standards

**Style & formatting:**
- Ruff with ALL rules selected, then specific ignores
- Line length: 100 characters
- PEP 257 docstrings with Sphinx-style (`:param:` syntax)
- Format line ending: LF
- Codespell for spell checking

**Type hints:**
- Full strict mypy
- Some provider modules (Apple Music, Sonos, YTMusic, etc.) have relaxed mypy

**Testing:**
- pytest with asyncio auto mode
- Coverage target: `music_assistant` module

---

## Lint & Test

```bash
ruff check <files>
ruff format --check <files>
python -m mypy <files>
codespell <files>
python -m pytest tests/ --asyncio-mode=auto
```

---

## PR Requirements

Target: `main`

- Description and rationale
- Link to related issue
- Test plan

---

## HAOS Dev Addon Testing

After pushing your branch, you can test on a Home Assistant instance using the Music Assistant **Dev** addon from `music-assistant/home-assistant-addon`.

Set `server_repo` in the addon config:

| Input Format | What It Resolves To |
|---|---|
| `""` (empty) | Latest nightly release (fastest startup) |
| `main` | `music-assistant/server@main` |
| `feat/my-feature` | Branch on main repo |
| `pr-456` | PR #456 (`refs/pull/456/head`) |
| `someuser/server@branch` | Fork + branch |
| `abc123def456` | Specific commit SHA |

Example:
```yaml
server_repo: "pr-123"
frontend_repo: ""
```

**Note:** Leave `frontend_repo` empty to use the bundled frontend (fastest startup).
