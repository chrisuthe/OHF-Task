---
name: start-ohf-task
description: Start a new task for an Open Home Foundation project (Home Assistant, ESPHome, Music Assistant, SendSpin, OHF-Voice) by creating a git worktree with a feature branch, applying the correct project-specific coding standards. Use when the user says "start ohf task", "start ha task", "start esphome task", "start sendspin task", "new ohf task", or similar.
user_invocable: true
---

# Start OHF Task

When this skill is invoked, follow these steps exactly.

---

## 0. Ensure you're in a git repository

```bash
git rev-parse --show-toplevel 2>/dev/null
```

If this fails, ask the user: **"Which OHF repository should I use? (Provide a path or repo name — e.g. `core`, `esphome`, `server`, `frontend`, `sendspin-cli`, `aiosendspin`)"**

Do NOT use AskUserQuestion — just ask in plain text and wait for their reply.

---

## 1. Detect the OHF project and load standards

Identify which Open Home Foundation project this repository belongs to by checking the remote URL:

```bash
git remote get-url origin 2>/dev/null
```

Map to a project and **read the corresponding sub-skill file** from the `skills/` directory next to this file. Also read `skills/shared.md` — it applies to ALL projects.

### Project → Sub-skill mapping

| Remote matches | Sub-skill to read | Base Branch |
|---|---|---|
| `home-assistant/core` | `skills/ha-core.md` | `dev` |
| `home-assistant/frontend` | `skills/ha-frontend.md` | `dev` |
| `esphome/esphome` | `skills/esphome.md` | `dev` |
| `music-assistant/server` | `skills/ma-server.md` | `dev` |
| `music-assistant/frontend` | `skills/ma-frontend.md` | `main` |
| `music-assistant/mobile-app` | `skills/ma-mobile.md` | `main` |
| `music-assistant/desktop-app` | `skills/ma-desktop.md` | `main` |
| `music-assistant/models` | `skills/ma-server.md` | `main` |
| `home-assistant/intents` | `skills/ohf-voice.md` | `main` |
| `OHF-Voice/*` | `skills/ohf-voice.md` | `main` |
| `sendspin/*` | `skills/sendspin.md` | `main` |

**Do not assume a base branch from this table alone** — the user may be working
from a fork whose remote is `<their-user>/<repo>`, and forks can lag or rename
branches. Confirm the branch exists before creating the worktree:

```bash
git rev-parse --verify origin/<base-branch>
```

If it does not resolve, fall back to the remote's own default and tell the user
which base you used and why:

```bash
git symbolic-ref refs/remotes/origin/HEAD --short   # e.g. origin/main
```

`models` and `intents` are `main`, not `dev`, despite sitting alongside repos
that do use `dev` — checking is cheaper than guessing wrong.

If the repo doesn't match a known project, inform the user and ask which project's conventions to follow.

**Read exactly two files:**
1. `skills/shared.md` (always)
2. The matching project sub-skill from the table above

These contain the coding standards, lint commands, PR requirements, and research hints for this specific project. Follow them for all subsequent steps.

---

## 2. Get the task name

**If the user provided a GitHub issue URL** (e.g. `https://github.com/home-assistant/core/issues/12345`):

Extract the issue number and fetch it:

```bash
gh issue view <number> --json number,title,state,author,body,labels,assignees
```

Derive the task name from the issue title. Strip common prefixes like `Feat:`, `Fix:`, `Bug:`, `Chore:`, `Refactor:` before converting to kebab-case.

Display the issue summary (title, state, author, description, labels) so the user can confirm.

**If no URL was provided**, ask the user: **"What should this task be called? (You can also paste a GitHub issue URL)"**

Do NOT use AskUserQuestion — just ask in plain text and wait for their reply.

---

## 3. Sanitize the name

Convert the task name to kebab-case:
- Lowercase everything
- Replace spaces and underscores with hyphens
- Remove any characters that aren't alphanumeric or hyphens
- Collapse consecutive hyphens into one
- Trim leading/trailing hyphens

---

## 4. Create the worktree

```bash
git fetch origin
git worktree add ../<repo-name>-<name> -b <branch-prefix>/<name> origin/<base-branch>
```

**Branch prefix:**
- `feat/` by default
- `fix/` if the issue has a `bug` label
- SendSpin repos also support: `refactor/`, `test/`, `chore/` (conventional commits)

If the branch already exists, inform the user and ask if they want to pick a different name or check out the existing branch.

---

## 5. Switch working directory

```bash
cd <absolute-path-to-worktree>
```

---

## 6. Confirm worktree creation

Tell the user:
- The worktree has been created at `../<repo-name>-<name>`
- They are on branch `<prefix>/<name>` based off `<base-branch>`
- All work will happen in the worktree, isolated from the main checkout

---

## 7. If an issue was provided: research, implement, and create a PR

If the task was started from a GitHub issue URL, continue with the following steps. If the task was started from a plain name (no issue), stop here and remind the user they can remove the worktree when done.

### 7a. Read project conventions

Read the project's `CLAUDE.md` / `AGENTS.md` in the repo. Apply the standards from the sub-skill file you loaded in step 1.

### 7b. Research & orient the user

Explore the codebase using the "Research Hints" from the sub-skill file. Then follow the educational approach from `shared.md` — present a "lay of the land" briefing and wait for the user's questions before proceeding.

### 7c. Plan — and run it past the Sage

Form a candidate plan, then **consult the `ohf-sage` subagent before presenting it
as final** (see "Consult the OHF Sage" in `shared.md`). Fold its answer in, and
surface any rule it cites *with* the citation so the user sees the reasoning.

Skip this for `ha-core` and `ha-frontend` — the Sage does not speak for Home
Assistant.

Then follow the planning approach from `shared.md`. Present the plan for approval before writing code.

### 7d. Implement with explanation

Follow the implementation approach from `shared.md` — explain as you go, don't dump code. Use the coding standards, lint commands, and conventions from the sub-skill file.

### 7e. Lint, format, and type-check

Run the lint/test commands specified in the sub-skill file. Fix any issues before proceeding.

### 7f. Run tests

Run the test commands specified in the sub-skill file.

### 7g. Sage review, then final walkthrough

**First, have the `ohf-sage` subagent review the diff** against project standards
(see `shared.md`). Treat an objection as blocking until either the change is made
or the user explicitly overrules it — and state which happened in the walkthrough
below. Skip for `ha-core` / `ha-frontend`.

Then follow the walkthrough approach from `shared.md`. Wait for the user to confirm understanding before creating the PR.

### 7h. Push, then hand the PR to the user

**Do NOT run `gh pr create`, and do not open the PR by any other means.** Per the
OHF `AI_POLICY.md`, autonomous agents may not open pull requests or issues. Your
job ends at pushing the branch and preparing everything the user needs; *they*
open it.

1. Push the branch:
   ```bash
   git push -u origin <prefix>/<name>
   ```

2. Build the compare URL. Which form depends on whether `origin` is the upstream
   repo or the user's fork:
   ```bash
   git remote get-url origin
   git remote get-url upstream 2>/dev/null   # present => fork workflow
   ```

   - **Fork** (`origin` is `<you>/<repo>`, `upstream` is the OHF repo):
     `https://github.com/<upstream-owner>/<repo>/compare/<base-branch>...<your-user>:<prefix>/<name>?expand=1`
   - **Direct push access** (no `upstream` remote):
     `https://github.com/<owner>/<repo>/compare/<base-branch>...<prefix>/<name>?expand=1`

3. Print, as one copy-pasteable block:
   - the compare URL
   - the proposed PR **title**
   - the proposed PR **body**, formatted per the PR requirements in the sub-skill
     file, in a fenced block so it can be copied verbatim

4. Tell the user plainly: *"Open that URL, paste the title and body, and review
   before submitting. I will not open the PR for you."*

5. If the sub-skill file includes HAOS dev addon testing instructions (MA Server and MA Frontend do), provide those to the user.

### 7i. Clean up reminder

Remind the user that when they're done reviewing, they can remove the worktree:
```bash
git worktree remove ../<repo-name>-<name>
```
