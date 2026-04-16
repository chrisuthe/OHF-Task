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
| `music-assistant/server` | `skills/ma-server.md` | `main` |
| `music-assistant/frontend` | `skills/ma-frontend.md` | `main` |
| `music-assistant/mobile-app` | `skills/ma-mobile.md` | `main` |
| `music-assistant/desktop-app` | `skills/ma-desktop.md` | `main` |
| `OHF-Voice/*` | `skills/ohf-voice.md` | `main` |
| `sendspin/*` | `skills/sendspin.md` | `main` |

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

### 7c. Plan

Follow the planning approach from `shared.md`. Present the plan for approval before writing code.

### 7d. Implement with explanation

Follow the implementation approach from `shared.md` — explain as you go, don't dump code. Use the coding standards, lint commands, and conventions from the sub-skill file.

### 7e. Lint, format, and type-check

Run the lint/test commands specified in the sub-skill file. Fix any issues before proceeding.

### 7f. Run tests

Run the test commands specified in the sub-skill file.

### 7g. Final walkthrough

Follow the walkthrough approach from `shared.md`. Wait for the user to confirm understanding before creating the PR.

### 7h. Create a PR

1. Push the branch:
   ```bash
   git push -u origin <prefix>/<name>
   ```

2. Create the pull request using `gh`:
   ```bash
   gh pr create --title "<title>" --body "<body>"
   ```

   Use the PR requirements from the sub-skill file to format the body correctly.

3. Display the PR URL to the user.

4. If the sub-skill file includes HAOS dev addon testing instructions (MA Server and MA Frontend do), provide those to the user.

### 7i. Clean up reminder

Remind the user that when they're done reviewing, they can remove the worktree:
```bash
git worktree remove ../<repo-name>-<name>
```
