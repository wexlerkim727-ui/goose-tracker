# AGENTS.md

Modern AI coding agent rules for this repository.

Applies to Codex, Claude Code, GitHub Copilot, OpenAI Agents, and other AI coding agents that read repository instructions.

This repository is a single-page browser tool (a 鹅鸭杀 / Goose Goose Duck card tracker). It is plain HTML + JavaScript with **no build system**, managed with Git, and maintained by a **single developer**. Keep it simple, fast, and easy to open directly in a browser.

## Project Facts

- Pure HTML + JavaScript + CSS in a single page. No framework, no bundler, no package manager.
- No build, install, or compile step. The app runs by opening the HTML file in a browser.
- Git is the only tooling dependency.
- Single-developer project — optimize for clarity and low overhead, not team process.

## Operating Principles

1. Finish the requested task with minimal back-and-forth.
2. Work autonomously when the next step is safe and obvious.
3. Keep changes small, focused, and reversible.
4. Preserve the single-file, no-build structure. Do not introduce a framework or bundler.
5. Do not waste tokens by printing long logs or repeating obvious context.

## Autonomous Workflow

Default loop:

```text
Understand -> Inspect -> Plan -> Edit -> Verify -> Review -> Report
```

Run this loop until the task is complete; repeat steps as needed. Do not pause between steps unless a pause condition applies.

Before editing, inspect relevant context automatically.
**Skip any file or directory below that does not exist — do not ask about it.**

1. `git status --short --branch`
2. The main HTML file and its inline/linked JS and CSS
3. `AGENTS.md`
4. `README.md`
5. `.specs/` files relevant to the task
6. `.memory/` files relevant to the task
7. Existing code related to the task

## When To Ask The User

Ask only when required for:

1. API keys, tokens, credentials, passwords, or secrets
2. Login, MFA, Captcha, payment, or account permission changes
3. Product or business decisions that cannot be inferred
4. Destructive actions such as deleting features or resetting Git history
5. Overwriting user changes
6. Adding a new dependency or external service

Otherwise, make the safest reasonable assumption and mention it in the final report.

For merely underspecified (not unsafe) requirements, do not ask: choose the most conventional interpretation, proceed, and record the assumption in the final report.

## Token Optimization

1. Prefer concise progress updates.
2. Do not paste full files unless asked.
3. Summarize command output instead of dumping logs.
4. Search with `rg` before opening large files.
5. Read only the files needed for the task.
6. Avoid repeated explanations.
7. Report only what changed, why, how it was verified, and what remains.

## Editing Rules

1. Modify the fewest files possible.
2. Do not change unrelated code.
3. Do not delete existing behavior without approval.
4. Do not create placeholder features.
5. Keep the app working as a single page that opens directly in a browser.
6. Prefer existing conventions and vanilla JS over new abstractions or libraries.
7. Keep empty template directories with `.gitkeep` when needed.

## Dependencies And Environment

This project has no build system. Default to zero tooling.

When the project clearly supports it, agents may automatically:

1. Run any existing project scripts, formatters, or linters that are already configured.
2. Open the HTML file in a browser for verification.
3. Stop any local processes started during verification.

Rules:

- Install only dependencies that already appear in an existing lockfile or manifest.
- Introducing a **new** package, framework, bundler, database, or external service requires user approval (see "When To Ask The User").
- Apply formatters and `--fix` / auto-write modes **only to files changed in the current task**. Never run repo-wide reformatting unless the user asks.

## Error Recovery

When something fails:

1. Read the exact error.
2. Form the most likely hypothesis.
3. Apply the smallest fix.
4. Re-run the relevant check.
5. Repeat for at most 3 consecutive attempts on the same failure.

After 3 failed attempts, stop and report the blocker, what was tried, and the next best option.

## Verification

1. Run relevant checks when available.
2. Only claim success for commands that actually ran and passed.
3. If no check exists, say that validation was limited to inspection.
4. Keep verification output short.
5. Do not invent test, build, lint, or runtime results.

For this project, verification usually means:

- `git status --short --branch`
- Reading the changed HTML/JS/CSS for correctness
- Opening the page in a browser for a manual smoke test when applicable
- Project-specific lint/format checks only if they are already configured

A task is done when the requested change is made, relevant checks pass (or their limits are stated), and the Final Report is produced.

## Safety (Destructive Actions)

Never run destructive shell or filesystem commands without explicit user approval. This includes `rm -rf`, recursive deletes, moving or overwriting existing tracked files, force flags, and redirecting output (`>`) over files you did not create in this task. When unsure whether an action is reversible, treat it as destructive and ask.

Do not pipe remote scripts into a shell (for example `curl ... | sh`) or fetch unlisted remote URLs without approval.

## Git Workflow

1. Check Git status before editing.
2. Never overwrite user changes.
3. Do not run destructive Git commands unless explicitly requested.
4. Do not commit unless the user asks.
5. Do not push unless the user asks.
6. Use the exact commit message requested by the user. If the user asks to commit without specifying a message, write a concise conventional commit message summarizing the change — do not stop to ask.
7. If committing, stage only relevant files.
8. Report whether the working tree is clean after finishing.

## Security

Never read, print, modify, commit, or push secrets.

Protected examples:

- `.env`
- `.env.*`
- API keys
- access tokens
- private keys
- passwords
- production credentials
- service account files

Markdown may reference environment variable names, but must not contain real secret values.

Before committing or pushing, check that sensitive files are not staged.

## Communication Style

1. Respond in the user's language.
2. Be direct and concise. No flattery, filler, or restating the request.
3. Do not narrate routine steps; surface decisions, assumptions, and blockers.
4. Use the Final Report format when a task is complete.

## Memory

Use `.memory/` (optional, create only when useful) to reduce repeated context, for example:

- `.memory/decisions.md` for decisions
- `.memory/coding-style.md` for style rules
- `.memory/mistakes.md` for mistakes to avoid
- `.memory/lessons.md` for lessons learned

Update memory only when it helps future work and is relevant to the current task.

## Specs

Use `.specs/` (optional) for intended requirements and design, for example:

- `.specs/requirements.md`
- `.specs/ui.md`
- `.specs/workflow.md`

Specs describe intent. They must not contain secrets.

## Final Report

Keep the final report short and use only:

```text
Modified Files

Reason

Validation

Remaining Issues
```
