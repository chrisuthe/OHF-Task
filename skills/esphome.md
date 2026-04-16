# ESPHome

**Repo:** `esphome/esphome` | **Base branch:** `dev` | **Python 3.11 + C++**

---

## Research Hints

- Identify whether changes are Python-side (config/codegen) or C++-side (components) as they have different conventions.
- Explain how the Python config validation generates C++ code and how the two sides connect.

---

## Coding Standards

**Python side:**
- Ruff for linting and formatting
- Config keys as uppercase constants: `CONF_EXAMPLE_KEY = "example_key"`
- Single-use keys: define in component; 3+ component keys: migrate to shared `const/__init__.py`
- Use walrus operator for optional config gathering (except booleans)
- Do not use `AUTO_LOAD` for main platform components

**C++ side:**
- Google C++ Style Guide as baseline
- 2-space indent, 120-char line limit
- Naming: `lower_snake_case` (functions/vars), `UpperCamelCase` (classes), `UPPER_SNAKE_CASE` (constants), `trailing_underscore_` (fields)
- Prefix member access with `this->`
- Prefer `const` variables or enums over `#define`
- Prefer `protected` over `private` for fields (extensibility)
- `setup()`, `loop()`, `update()` must NEVER block (no delays >10ms)
- Use state machines for operations requiring time
- Implement `dump_config()` using `ESP_LOGCONFIG` exclusively
- NO heap allocation after `setup()` — use `std::array`, `StaticVector`, `FixedVector`
- Avoid `std::deque`, prefer `std::unique_ptr<uint8_t[]>` over `std::vector<uint8_t>`

**Breaking changes:**
- Require justification, migration guide, deprecation period (6 months when possible)
- Use `ESPDEPRECATED()` macro with "Remove before YYYY.MM.0" comment
- Blog post required for core/architectural changes

---

## Lint & Test

```bash
# Python side
ruff check <files>
ruff format --check <files>

# C++ side: clang-format is checked by CI
# Full local check:
# docker run --rm -v "${PWD}/":/esphome -it ghcr.io/esphome/esphome-lint script/quicklint

# Tests
python -m pytest tests/ --cov=esphome --cov-branch
```

---

## PR Requirements

Target: `dev`

- Description of changes
- Type: bugfix / new feature / breaking change / code quality
- Test environment (ESP32, ESP8266, RP2040, etc.)
- YAML config example if applicable
- Link to `esphome-docs` PR for user-facing changes
- For breaking changes: migration guide, justification, deprecation timeline
