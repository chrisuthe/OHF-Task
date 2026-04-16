# OHF-Voice (Intents, Voice, & i18n)

**Org:** `OHF-Voice` | **Base branch:** `main` | **YAML + Python + C++ + TensorFlow**

**Key repos:** `intents`, `hassil`, `piper1-gpl`, `speech-to-phrase`, `micro-wake-word`, `wyoming`

---

## Research Hints

- For **intents**: understand the HassIL template syntax. Explain how sentence templates, response templates, and test files relate to each other. Check `sentences/<lang>/_common.yaml` for shared lists and expansion rules.
- For **ML repos** (micro-wake-word, speech-to-phrase, piper): these have specialized ML training workflows. Explain the model architecture and training pipeline relevant to the change.

---

## Intents Repo Structure

```
sentences/<lang>/           # Sentence templates (YAML)
  _common.yaml              # Shared lists, error responses, expansion rules
  <domain>_<Intent>.yaml    # Templates per domain+intent
responses/<lang>/           # Jinja2 response templates
tests/<lang>/               # Test sentences with expected matches
  _fixtures.yaml            # Test entity/area fixtures
```

## HassIL Template Syntax

- `(red|green|blue)` — alternatives
- `[the]` — optional word
- `{list_name}` — slot/list reference (populates intent slots)
- `{list_name:slot_name}` — list with different slot name
- `<rule_name>` — expansion rule reference (defined in `_common.yaml`)
- `(patience; you must have)` — permutations (semicolons)

---

## Coding Standards (Intents Repo)

- YAML files formatted with Prettier (via pre-commit hooks)
- Python tooling: Black (line length 88), flake8, isort, mypy, pylint
- Always add corresponding test files when adding sentence templates
- Watch sentence count: `count_sentences --summary` to avoid combinatorial explosion from too many alternatives
- Use `llm_template` command to generate LLM translation prompts when translating intents to a new language
- Response templates use Jinja2 syntax

## Other OHF-Voice Python Repos (hassil, speech-to-phrase, wyoming)

- Standard Python tooling: Black, flake8, isort, mypy, pytest
- hassil is the intent parsing engine — changes here affect all language matching
- wyoming is the peer-to-peer protocol connecting voice components (STT, TTS, wake word)

## ML Training Repos (micro-wake-word, piper)

- micro-wake-word: TensorFlow/TFLite, MixConv architecture, 16kHz mono audio, training notebook at `basic_training_notebook.ipynb`
- piper: C++ neural TTS engine with voice datasets at `OHF-Voice/voice-datasets`
- speech-to-phrase: Constrained local STT, models from HuggingFace (`rhasspy/rhasspy-speech`)

---

## How Translations Work Across the OHF Ecosystem

| What | Where | Format | How to contribute |
|---|---|---|---|
| Voice command sentences | `OHF-Voice/intents` `sentences/<lang>/` | YAML | Direct GitHub PRs |
| Voice responses | `OHF-Voice/intents` `responses/<lang>/` | YAML + Jinja2 | Direct GitHub PRs |
| Frontend UI strings | `home-assistant/frontend` `src/translations/en.json` | JSON | English via PR, others via Lokalise |
| Core component strings | `home-assistant/core` `components/<name>/strings.json` | JSON | English via PR, others via Lokalise |
| MA Frontend strings | `music-assistant/frontend` | JSON | Lokalise (vue-i18n) |

---

## Lint & Test

**Intents repo:**
```bash
python3 -m script.intentfest validate --language <lang>
python3 -m script.intentfest parse --language en --sentence "turn on the lights"
python3 -m script.intentfest sample --language en -n 1
python3 -m script.intentfest count_sentences --language <lang> --summary
python3 -m script.intentfest llm_template <lang> <IntentName>
python3 -m script.intentfest add_language <code> <name>
pytest tests --language <lang> -k <test_name>
```

**Other Python repos:**
```bash
black --check <files>
flake8 <files>
isort --check <files>
python -m mypy <files>
pytest tests/
```

---

## PR Requirements

Target: `main`

- Description of changes
- Language(s) affected
- Link to related issue
- For intents: include test files for new sentence templates
- For translations: verify sentence count isn't exploding (`count_sentences --summary`)
