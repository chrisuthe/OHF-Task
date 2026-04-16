# Shared OHF Standards

This file is loaded for ALL OHF projects. It contains the educational approach and universal contribution principles.

---

## Educational Approach

> **IMPORTANT:** The goal of this skill is NOT just to produce code. The person invoking it should walk away understanding every line that was generated, why it was written that way, and how it fits into the broader codebase. At every stage, describe what you're doing and why, show how the pieces connect, and check that the user is following along. Treat this as pair programming with a learning focus, not a code delivery service.

### During Research (7b) — Orient the user

Present a "lay of the land" briefing:
- A plain-language summary of the area of the codebase being touched
- Which files matter and what each one does (not just a list of paths — explain the role of each)
- How data flows through the relevant components
- Any patterns the codebase uses that the user should understand
- Existing code that is similar to what we'll be writing (reference it as a model)

**Ask the user:** "Does this make sense? Any questions about the existing code before we plan the changes?"

Wait for their response before proceeding.

### During Planning (7c)

1. **Describe the approach in plain language first** — what are we doing and why this way?
2. **Show the file-by-file breakdown** — for each file, explain its role, what changes, and how it connects
3. **Call out design decisions** — explain tradeoffs and why you chose this approach
4. **Highlight patterns being followed** — "We're following the same pattern as `<existing_file>` because..."

### During Implementation (7d) — Explain as you go

For each logical chunk of code written, pause and explain:
- **What it does** — walk through the code in plain language
- **Why it's structured this way** — connect it to the project's conventions
- **How it fits** — explain how this piece interacts with the rest of the system
- **Key decisions** — call out anything non-obvious

After completing each file or significant section, ask:
- "Does this make sense? Want me to walk through any part in more detail?"

**Do NOT just dump code and move on.** The user should be able to explain every file in the PR to a reviewer.

### Before PR Creation (7g) — Final walkthrough

Present a "PR preview" covering:
1. **Summary of what changed and why** — 2-3 sentence overview
2. **File-by-file walkthrough** — role, changes, patterns, connections
3. **Architecture diagram** (when helpful)
4. **What a reviewer will look for**
5. **What to test manually**

Then ask:
- "Can you walk me through what this PR does in your own words?"
- "Any parts you'd like me to explain differently?"
- "Ready to create the PR, or do you want to change anything first?"

**Wait for their response.** Do NOT create the PR until the user confirms.

---

## Commit Message Guidelines (All Projects)

- Do NOT amend, squash, or rebase commits after opening a PR (preserves review trail)
- Keep commits focused on single logical changes
- Reference the issue number where relevant
- Write commit messages that explain the "why" — the diff shows the "what"

---

## General OHF Contribution Principles

These apply across all Open Home Foundation projects:

1. **Keep PRs small and focused** — one logical change per PR
2. **Do not amend commits after opening a PR** — preserves the review trail
3. **Create a feature branch per PR** — never commit directly to `dev`/`main`
4. **Test locally before submitting** — CI is a safety net, not a testing strategy
5. **Document user-facing changes** — each project has a companion docs repo
6. **Review others' PRs** — HA Core explicitly encourages reviewing 2 other open PRs
7. **No guarantee of merge** — bug fixes and code quality improvements are prioritized over new features
8. **Review comments are not personal** — expect constructive feedback; PRs auto-convert to draft when changes are requested
