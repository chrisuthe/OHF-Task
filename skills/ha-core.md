# Home Assistant Core

**Repo:** `home-assistant/core` | **Base branch:** `dev` | **Python >=3.14**

---

## Research Hints

- Check the Integration Quality Scale tier in `manifest.json` and reference Platinum/Gold integrations as examples of best practice. Explain what the quality scale means and why we're referencing a particular tier.
- If touching translations, explain the `strings.json` format and cross-reference syntax (`[%key:component::domain::section::key%]`).

---

## Coding Standards

**Style & formatting:**
- Ruff for linting and formatting (migrated from Black)
- PEP 8 strict compliance
- Google-style docstrings (with Args, Returns, Raises sections)
- Comments are full sentences ending with a period
- Constants, list contents, and dict entries in alphabetical order
- Imports ordered (managed by Ruff/isort)

**Logging:**
- Do NOT include platform/component name in log messages (added automatically)
- No periods at end of log messages
- Never log secrets (API keys, tokens, passwords)
- Use `_LOGGER.debug` for non-user-facing messages
- Use percentage formatting for logging (`_LOGGER.debug("Value: %s", val)`)
- Use f-strings everywhere else

**Type hints:**
- Fully type all code
- Modules in `.strict-typing` get strict mypy checking
- Use `assert` only in `TYPE_CHECKING` blocks to narrow types

**Architecture:**
- All external device/service communication must be in a separate pypi library
- Config flows required for UI setup (YAML-only is legacy)
- Follow the Integration Quality Scale — reference Gold/Platinum integrations
- Comply with ADRs in the `home-assistant/architecture` repo
- Max 5 open PRs per contributor

**Python version:** >=3.14 (supports `except TypeA, TypeB:` without parens)

---

## Lint & Test

```bash
ruff check <files>
ruff format --check <files>
python -m mypy <files>  # if files are in .strict-typing
python -m pylint <files>
python -m script.hassfest
python -m pytest tests/<component_dir>/ --asyncio-mode=auto
```

- All test parameters require type annotations
- Use specific types: `HomeAssistant`, `MockConfigEntry`, not `Any`

---

## PR Requirements

Target: `dev`

- Description of any breaking changes
- Type: dependency upgrade / bugfix / new integration / new feature / deprecation / breaking change / code quality
- Link to related issue (`Closes #<number>`)
- Link to documentation PR on `home-assistant.io` if user-facing
- Confirm: code tested locally, Ruff formatted, dev guidelines followed
- For integrations: updated `manifest.json` and run `python -m script.gen_requirements_all`
