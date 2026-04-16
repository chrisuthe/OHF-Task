# SendSpin (All Repos)

**Org:** `sendspin` | **Base branch:** `main` | **Multi-language (Python, Go, Rust, TS, C++, Swift, .NET)**

SendSpin is an open standard protocol for synchronized multi-room audio streaming, developed under the Open Home Foundation. It defines five client roles: **Player**, **Controller**, **Metadata**, **Artwork**, and **Visualizer**. Communication uses WebSockets with JSON control messages and binary audio/visual data. Audio uses 20ms chunks with microsecond timestamps.

**Repos:** sendspin-cli, aiosendspin, sendspin-go, sendspin-js, sendspin-rs, sendspin-cpp, SendspinKit, sendspin-dotnet, sendspin-vst, spec, conformance, cast, time-filter, sync-test, sendspin-jack-bridge, sendspin-audio.com, backlog

---

## Research Hints

- Read the repo's `CLAUDE.md` or `AGENTS.md` first — many SendSpin repos have detailed architectural guidance for AI agents.
- Explain the protocol roles (Player, Controller, Metadata, Artwork, Visualizer) if relevant to the change.
- Check the `conformance` repo for protocol test expectations.

---

## Cross-Cutting Standards (All Repos)

- **Conventional commits:** `feat:`, `fix:`, `refactor:`, `test:`, `chore:`
- All new user-facing features must be documented in README.md
- Pre-commit hooks are used consistently
- License: Apache-2.0 (except sendspin-rs which is dual MIT/Apache-2.0)
- Many repos have `CLAUDE.md` / `AGENTS.md` with architectural guidance — **always read these first**
- The `conformance` repo contains cross-implementation protocol tests

---

## Per-Language Standards

### Python (sendspin-cli, aiosendspin, conformance, sync-test, sendspin-jack-bridge)
- Python >=3.12, strict typing everywhere
- Ruff with `select = ["ALL"]` (all rules enabled, ~25 annoying rules excluded), 100-char line length
- Google-style docstrings (PEP 257)
- LF line endings
- MyPy in strict mode
- pytest with pytest-asyncio (auto mode), pytest-xdist for parallel runs, 10-second timeout
- syrupy for snapshot testing
- codespell for spell checking
- isort for imports
- aiosendspin uses `ManualClock` for deterministic timing in tests

### Go (sendspin-go)
- Go 1.24+
- golangci-lint (errcheck and staticcheck disabled)
- Build via Makefile: `make`, `make test`, `make lint`, `make conformance`
- Convention: `ABOUTME:` comments at file headers describing purpose
- Directory structure: `pkg/` (public), `internal/` (private), `cmd/` (entry points)
- Co-located `_test.go` test files
- TUI built with Bubble Tea + Lip Gloss

### Rust (sendspin-rs, sendspin-vst)
- Dual license: MIT OR Apache-2.0
- Build: Cargo with `cargo make verify` (tests + Clippy + docs + format checks)
- Release profile: LTO enabled, opt-level 3, single codegen unit
- Async runtime: Tokio
- Audio: cpal

### TypeScript (sendspin-js)
- Node >=16, ES modules
- ESLint with TypeScript parser + Prettier
- Husky git hooks
- Bundler: Rollup
- Convention: always update the sample player when adding new SDK features

### C++ (sendspin-cpp, time-filter)
- CMake for host builds, ESP-IDF Component Registry for ESP32
- clang-format, clang-tidy, clangd for code quality
- Pre-commit hooks + markdown linting
- Target platforms: host (macOS, Linux) and ESP-IDF v5.1+ (ESP32)

### Swift (SendspinKit)
- Swift 6.0+, targets iOS 17+ / macOS 14+ / tvOS 17+ / watchOS 10+
- SwiftLint + SwiftFormat for code quality
- DocC for documentation
- Swift Package Manager for distribution

### .NET (sendspin-dotnet)
- C# / .NET SDK

---

## Lint & Test

```bash
# Python
ruff check <files> && ruff format --check <files> && python -m mypy <files> && codespell <files>
python -m pytest tests/ --asyncio-mode=auto -x --timeout=10

# Go
make lint && make test

# Rust
cargo make verify

# TypeScript
npx eslint . && npx prettier --check . && npm test

# C++
pre-commit run --all-files

# Swift
swift build && swift test
```

---

## PR Requirements

Target: `main`

- Description and rationale
- Link to related issue
- Conventional commit style in PR title (e.g. `feat: add opus codec support`)
- All new user-facing features must be documented in README.md
