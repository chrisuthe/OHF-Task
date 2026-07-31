# Music Assistant Server

**Repo:** `music-assistant/server` | **Base branch:** `dev` | **Python 3.12**

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

### Hook runner: `pre-commit`

```bash
pre-commit run --all-files
```

This is the gate, and it runs custom hooks the individual tools below do not
cover — notably `check_method_order` (public methods before private, per class),
`check_blocking_io` and `check_test_layout`. Expect those to catch things `ruff`
and `mypy` are perfectly happy with.

(Contrast `home-assistant/core`, which uses `prek` — `pre-commit` there silently
bypasses the real runner. Don't carry the habit between repos.)

### Individual tools

Useful for tight loops on specific files. Finish with the hook runner above.

```bash
ruff check <files>
ruff format --check <files>
python -m mypy <files>
codespell <files>
python -m pytest tests/ --asyncio-mode=auto
```

---

## PR Requirements

Target: `dev`

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
| `dev` | `music-assistant/server@dev` |
| `feat/my-feature` | Branch on dev repo |
| `pr-456` | PR #456 (`refs/pull/456/head`) |
| `someuser/server@branch` | Fork + branch |
| `abc123def456` | Specific commit SHA |

Example:
```yaml
server_repo: "pr-123"
frontend_repo: ""
```

**Note:** Leave `frontend_repo` empty to use the bundled frontend (fastest startup).
