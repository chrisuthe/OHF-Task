# Getting started with start-ohf-task

This guide walks you through installing the `start-ohf-task` skill and using it to open your first Open Home Foundation pull request.

---

## Prerequisites

You'll need:

- **Claude Code** — installed and working. See [claude.com/claude-code](https://claude.com/claude-code).
- **git** — 2.20+ (for `git worktree`).
- **GitHub CLI (`gh`)** — authenticated to github.com. Run `gh auth status` to check; `gh auth login` to fix.
- **A local clone** of whichever OHF project you're contributing to (e.g. `home-assistant/core`, `esphome/esphome`, `music-assistant/server`, etc.). You invoke the skill from inside that clone.

You don't need anything else — no Python venv, no npm install, no build step. The skill is pure Markdown.

---

## Installation

### The easy way: ask Claude to install it

Open Claude Code in any directory and say:

> Please install the `start-ohf-task` skill from `https://github.com/chrisuthe/OHF-Task` into my Claude skills directory.

Claude will clone the repo into `~/.claude/skills/start-ohf-task/` (on Windows: `C:\Users\<you>\.claude\skills\start-ohf-task\`). That's the directory Claude Code scans for skills on startup.

### The manual way

```bash
# macOS / Linux
git clone https://github.com/chrisuthe/OHF-Task ~/.claude/skills/start-ohf-task

# Windows (PowerShell or Git Bash)
git clone https://github.com/chrisuthe/OHF-Task "$env:USERPROFILE\.claude\skills\start-ohf-task"
```

### Verifying the install

In a new Claude Code session, ask:

> What skills are available?

You should see `start-ohf-task` in the list with a description mentioning Home Assistant, ESPHome, Music Assistant, SendSpin, and OHF-Voice.

### Updating later

```bash
cd ~/.claude/skills/start-ohf-task
git pull
```

Or just ask Claude: "Please update my `start-ohf-task` skill."

---

## Using the skill

### Start from inside a cloned OHF repo

```bash
cd ~/code/home-assistant-core   # or whichever OHF repo
claude                          # launch Claude Code here
```

Then say one of:

- `start ohf task`
- `start ha task`
- `start esphome task`
- `start ma task`
- `start sendspin task`
- `new ohf task`
- `/start-ohf-task` (if your Claude build supports slash invocation of skills)

### With a GitHub issue URL (recommended)

The skill does much more when you give it an issue. Paste the URL:

> start ha task https://github.com/home-assistant/core/issues/123456

The skill will:

1. Detect you're in `home-assistant/core` from the git remote.
2. Load the HA Core coding standards (`skills/ha-core.md`) plus universal OHF principles (`skills/shared.md`).
3. Fetch the issue, show you the title/body/labels, and confirm the task name with you.
4. Create a worktree at `../core-<kebab-name>` on a new branch `feat/<kebab-name>` (or `fix/<kebab-name>` if the issue has a `bug` label), based off `origin/dev`.
5. Present a "lay of the land" briefing — which files matter, how data flows, existing code to model after — and wait for your questions.
6. Plan the change with you, file-by-file, before writing any code.
7. Implement in chunks, pausing after each logical section to explain what it did and why.
8. Run `ruff`, `mypy` (if applicable), `pylint`, `hassfest`, and `pytest` against the touched files.
9. Do a full PR walkthrough and ask you to explain the change back in your own words before pushing.
10. Push the branch, open a PR with a correctly-formatted body, and print the PR URL.

### Without an issue (just a named task)

> start ma task "fix spotify connect reconnect loop"

The skill will set up the worktree and branch, load MA Server standards, and stop there. You drive the rest of the work yourself — useful when you're exploring before you're ready to commit to a PR.

### If you're not in a git repo

The skill will ask which OHF project you want to work on and route accordingly. But you'll get a much better experience by `cd`-ing into your clone first.

---

## What happens after the PR is open

The skill reminds you that **you should not amend, squash, or rebase commits after opening the PR** — it destroys the review trail. Every requested change goes in as a new commit on the same branch; maintainers squash on merge.

When the PR is merged (or closed), clean up the worktree:

```bash
git worktree remove ../<repo>-<task-name>
```

---

## Troubleshooting

**"The skill didn't activate when I asked."**
Make sure the directory is exactly `~/.claude/skills/start-ohf-task/` with `SKILL.md` at its root. Restart your Claude Code session so it re-scans.

**"It picked the wrong base branch."**
Open an issue — the base-branch mapping lives in `SKILL.md` under "Project → Sub-skill mapping" and is easy to correct.

**"`gh issue view` failed."**
Run `gh auth status`. If the repo is private (shouldn't be for OHF projects), make sure your token has `repo` scope.

**"My project isn't in the list."**
If it's an OHF project that should be supported, please open an issue or PR against [`skills/`](./skills/) adding a sub-skill file and a mapping row in `SKILL.md`.

**"The worktree path already exists."**
Either you have a leftover from a previous run (`git worktree list` to see them, `git worktree remove <path>` to clean up), or you picked a name that collides. Re-run with a different task name.

---

## Learning more

- [README.md](./README.md) — what this skill is and why it exists, for project maintainers
- [SKILL.md](./SKILL.md) — the actual instructions Claude follows, step by step
- [skills/shared.md](./skills/shared.md) — universal OHF contribution principles
- [skills/](./skills/) — per-project coding standards, lint commands, and PR requirements
