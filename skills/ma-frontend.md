# Music Assistant Frontend

**Repo:** `music-assistant/frontend` | **Base branch:** `main` | **Vue 3 + TypeScript**

---

## Research Hints

- Check if the component uses Vuetify (legacy) or shadcn-vue (new). All new components MUST use shadcn-vue. Explain the migration context so the user understands why.
- Check for existing composables in `src/composables/` and helpers in `src/helpers/`.

---

## Coding Standards

**Framework & tooling:**
- Vue 3 with Composition API + TypeScript (strict mode)
- Package manager: **Yarn 1** (v1.22.22, pinned in `packageManager` field)
- Build: **Vite** with dev server on port 3000
- Styling: **Tailwind CSS v4** with utility classes (`tailwind-merge`, `clsx`, `class-variance-authority`)
- UI library: **shadcn-vue** (built on Reka UI) — **ALL new components MUST use shadcn-vue, NOT Vuetify**
  - Vuetify is legacy and being phased out
  - shadcn-vue components live in `src/components/ui/`
- Linter: ESLint 8 with TypeScript parser + Prettier integration
- Tests: **Vitest** with `happy-dom` environment, `@vue/test-utils`
- Git hooks: **Husky** pre-commit runs `yarn lint` + `yarn test:run` (both must pass)

**Code style rules:**
- **Double quotes** (not single quotes)
- 2-space indent for JS/TS/Vue, 4-space for JSON
- LF line endings
- Components under 300-400 lines, single responsibility
- All props and emits must have explicit TypeScript type definitions — implicit `any` is prohibited
- Use `interface` for object shapes, `type` for unions/intersections
- Every function parameter and return value must be typed
- Move complex template expressions to computed properties
- Clean up event listeners and watchers in `onUnmounted`
- Use semantic HTML with appropriate ARIA attributes

**Project structure:**
- `src/composables/` — reusable stateful logic (Vue composables)
- `src/helpers/` — pure utility functions, one concern per file (e.g. `string.ts`, `date.ts`)
  - Helper files **REQUIRE** colocated unit tests: `src/helpers/string.test.ts`
- `src/components/ui/` — shadcn-vue components
- Path aliases: `@/*` -> `./src/*`, `@components/*`, `@views/*`, `@layout/*`, `@plugins/*`

**User feedback:**
- Every API call must have toast notifications (success + meaningful error messages)
- Console logging is NOT a substitute for user-facing error feedback

**Translations:**
- Managed via Lokalise (vue-i18n for runtime)

**Key dependencies:**
- `zod` for schema validation
- `@tanstack/vue-form` + `@tanstack/vue-table` for forms/tables
- `ts-pattern` for pattern matching
- `websocket-ts` for WebSocket communication
- `mitt` for event bus

---

## Lint & Test

```bash
yarn lint          # ESLint with --fix (includes Prettier)
yarn test:run      # Vitest single run
yarn test:coverage # With coverage report
yarn build         # Verify production build succeeds
```

---

## PR Requirements

Target: `main`

- Description and rationale
- Note whether components use shadcn-vue (required for new work) vs Vuetify (legacy)
- Link to related issue
- Confirm: lint and tests pass (`yarn lint`, `yarn test:run`)

---

## HAOS Dev Addon Testing

After pushing your branch, you can test on a Home Assistant instance using the Music Assistant **Dev** addon from `music-assistant/home-assistant-addon`.

Set `frontend_repo` in the addon config:

| Input Format | What It Resolves To |
|---|---|
| `""` (empty) | Skip frontend build, use bundled (fastest) |
| `main` | `music-assistant/frontend@main` |
| `feat/my-feature` | Branch on main repo |
| `pr-789` | PR #789 (`refs/pull/789/head`) |
| `someuser/frontend@branch` | Fork + branch |

Example:
```yaml
server_repo: ""
frontend_repo: "pr-789"
```

**Note:** Setting `frontend_repo` triggers a full `yarn install && yarn build` in the addon, which is slower. Leave it empty to use the bundled frontend when only testing server changes.
