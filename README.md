# discuss-and-plan-before-coding

A [Claude Code](https://claude.com/claude-code) skill that runs a structured discovery-and-planning workflow **before any code gets written**.

It walks through:
- understanding the real problem (not the proposed solution),
- a hard gate on whether building software is even the right call,
- architecture, UX, and stack discussion,
- writing the decisions into `SPEC.md` and `PLAN.md` in the repo (files, not chat, are the source of truth),
- task-by-task execution that keeps those files in sync with the code,
- a lightweight, opt-in self-improvement mechanism that lets the skill accumulate transferable lessons across projects over time — never silently, always via a tagged proposal the user approves.

See [`SKILL.md`](SKILL.md) for the full workflow.

> **⚠️ Experimental.** This is one person's personal workflow, shared as-is. Test it on your own projects before relying on it — phases, prompts, and the self-improvement mechanism may not fit every workflow or may have rough edges.

## Installation

Install it **globally**, not inside a single project:

```sh
git clone https://github.com/<your-username>/discuss-and-plan-before-coding.git ~/.claude/skills/discuss-and-plan-before-coding
```

This matters for two reasons:
1. Claude Code only discovers skills under `~/.claude/skills/` (user scope) or a project's local `.claude/skills/`.
2. The self-improvement mechanism (see `SKILL.md`) accumulates lessons in `references/lessons-learned.md` *across* projects. A project-scoped copy resets that memory every time you start a new repo, so install it globally to let it actually learn over time.

After installing, restart Claude Code (or start a new session) so it picks up the skill. If you plan to use the self-improvement mechanism (you almost certainly will, since it's automatic), also enable the pre-commit hook described below — it's the main safety net against accidentally pushing personal data.

## Repo layout

```
SKILL.md                       the skill itself — loaded every time it triggers
CHANGELOG.md                   dated log of changes to SKILL.md and lesson promotions/prunes
references/lessons-learned.md  accumulated transferable lessons (starts empty; not auto-loaded)
```

`lessons-learned.md` ships empty here on purpose — it fills up with *your* lessons as you use the skill across your own projects.

## Keeping your personal lessons out of git

`references/lessons-learned.md` is meant to fill up with your own accumulated lessons over time (see the self-improvement section in `SKILL.md`). If your local clone of this skill is itself a git repo (e.g. you cloned it straight into `~/.claude/skills/discuss-and-plan-before-coding`, as the install instructions above suggest), those personal edits live in the same file that's tracked in this repo's history — and a plain `git add .` / `git commit -a` / `git push` would include them.

`.gitignore` alone does **not** protect you here: it only affects files git has never tracked. This file *is* tracked (it ships with an empty template), so `.gitignore` has no effect on edits to it once it exists.

To actually keep your personal lessons local-only, run this once after cloning:

```sh
git update-index --skip-worktree references/lessons-learned.md
```

This tells git to ignore further changes to your local copy of that file — it won't show up in `git status`, `git diff`, or get swept up by `git add -A`/`git commit -a`, even though it's a tracked file. You can keep pulling updates to `SKILL.md` from upstream normally; this only affects the lessons file.

If you ever want to deliberately commit a change to it (e.g. contribute a lesson back upstream via a PR), reverse it first:

```sh
git update-index --no-skip-worktree references/lessons-learned.md
# commit your change
git update-index --skip-worktree references/lessons-learned.md   # re-enable afterward
```

## Pre-commit hook: catching accidental personal data

`SKILL.md` has a rule telling Claude never to write real project/client/account names into `references/lessons-learned.md` (see "Privacy" in `SKILL.md`). That rule only constrains Claude's behavior, though — it won't stop a human from typing something personal by hand, or from missing it during review. As a mechanical backstop that runs regardless of who's committing, this repo ships a pre-commit hook (`hooks/pre-commit`) that scans the lines you're about to commit for common patterns: email addresses, home-directory paths with a username, secret/API-key-looking assignments, and `Seen on:` lines that contain real names instead of a plain count.

Enable it once per clone:

```sh
git config core.hooksPath hooks
```

After that, any `git commit` that adds a matching line is blocked with the offending lines printed out, so you can fix them before they ever reach `git push`. It's a pattern scanner, not a guarantee — it won't catch, say, a real client name typed as plain prose with no surrounding marker. If you hit a false positive, bypass that one commit with:

```sh
ALLOW_PERSONAL_DATA=1 git commit ...
```

## Feedback

Feedback, bug reports, and suggestions about the **core workflow** (`SKILL.md`) are welcome via [GitHub Issues](../../issues). This is shared experimentally — if a phase doesn't make sense for your workflow, or the self-improvement mechanism misbehaves, please open an issue.

## License

MIT — see [LICENSE](LICENSE).
