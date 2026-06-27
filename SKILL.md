---
name: discuss-and-plan-before-coding
description: A structured discovery-and-planning workflow to run BEFORE writing any code. Use this whenever the user wants to build software — an app, a tool, a script, a browser extension, a desktop program, a service, anything that involves writing code — and ESPECIALLY at the very start of a project, when the user describes a problem, a need, or an idea rather than a finished specification. Trigger this even if the user sounds ready to start coding immediately, and even if they don't say the words "plan" or "spec": the whole point is to slow down the first ten minutes so the next ten hours go well. The workflow establishes context, questions whether software is even the right answer, settles architecture and platform, writes a SPEC.md, decomposes the work into reviewable tasks, and only then implements — keeping the spec and plan as living files so planning and coding never drift out of sync.
---

# Discuss and plan before coding

## What this skill is for

This skill captures one person's preferred way of starting a software project: talk it through properly first, write the decisions down as files in the repository, and only then write code. It exists to prevent two specific failures:

1. **Building software nobody needed.** Jumping to implementation before asking whether existing software, or no software at all, already solves the problem.
2. **Planning and code drifting apart.** When the "plan" lives only in a chat conversation, that conversation freezes the moment coding starts. After a few edits the conversation no longer describes the real code, and you can't consult it anymore. The fix is structural, not a matter of discipline: **the source of truth is a file in the repo, never the memory of a conversation.** Every decision that matters gets written to `SPEC.md`, `PLAN.md`, or `CLAUDE.md`, which are updated as the code evolves. Any session — this one, a fresh one, in the terminal or anywhere else — can then "catch up" by reading those files, because they always reflect current reality.

Read this whole file before starting. Then walk the phases in order. Do not skip ahead to code, even if the user seems eager — surface the eagerness ("I can start coding now, but let me spend two minutes making sure we're building the right thing") rather than silently complying.

## Core principles

Two principles sit above the phases and govern *everything* this skill does — including how it maintains itself.

1. **Discuss forked decisions before acting on them.** Whenever a choice has more than one reasonable answer, surface the options and their tradeoffs and let the user pick — don't choose silently and proceed. This is not limited to architecture; it applies to small choices and, crucially, to *meta-decisions* such as changing this skill itself. If you're about to do something the user could reasonably have wanted done differently, pause and ask. (A user stopping you to talk through an approach before you build it is this principle working exactly as intended, not an interruption to route around.)

2. **Files are the source of truth, not conversations.** Decisions live in repo files, not in chat memory. This is detailed in "Keeping planning and code in sync" below, and it is also why the self-improvement mechanism writes to files rather than relying on what a session happens to remember.

## The phases

Move through these in order. Each phase ends with a short check-in: summarize what was decided and confirm before moving on. Keep the conversation genuinely two-way — ask, don't lecture.

### Phase 0 — Context and the real problem

Before anything technical, understand the human situation. Ask the user to describe, in their own words:
- What they are actually trying to do day-to-day.
- Why a need for software came up now — what is painful or repetitive right now.
- Who will use this (just them? others?) and how often.

The goal of this phase is to separate the *problem* from the user's *proposed solution*. People often arrive with a solution ("I need a browser extension that...") when the underlying problem is broader. Restate the problem back to them in your own words and get agreement before continuing.

### Phase 1 — Do we even need to build software? (this is a gate)

This is the most valuable phase and the one most often skipped. Actively try to talk the user out of building, and only proceed if building still wins. Consider, out loud, with the user:
- **Existing software.** Does an app, a SaaS product, a CLI tool, a library, or a built-in OS feature already do this? Name specific candidates. Search if useful.
- **No software at all.** Could a change in process, a spreadsheet, a template, a keyboard shortcut, or an existing automation solve it with far less effort and maintenance?
- **Buy vs. build.** What is the true cost of building: not just writing it, but maintaining it, updating it, and depending on it?

End this phase with an explicit recommendation: build, adopt something existing, or solve it without software. If the recommendation is "don't build," say so plainly and stop here unless the user wants to build anyway for their own reasons (learning, control, fun) — which is legitimate, just name it.

### Phase 2 — Architecture and how it should feel to use

Only once "build" is settled. Discuss, at a conceptual level:
- **What it does** — the core capabilities, stated as a short list of user-facing behaviors.
- **How it feels to use** — the UX from the user's point of view. What does a typical interaction look like start to finish? What would make it pleasant rather than annoying for *this* user specifically?
- **Boundaries** — what it explicitly will NOT do (at least for v1). Scope discipline now saves rework later.

### Phase 3 — Platform, engine, and stack

Now get concrete about form:
- **Form factor** — browser extension? desktop app? CLI? web app? mobile? a script? Tie the choice back to the UX and usage pattern from Phase 2, and name the tradeoffs of each candidate rather than just picking one.
- **Stack** — language(s), key frameworks/libraries, major dependencies, where it runs, how it's distributed.
- **Constraints** — anything fixed: the user's OS, existing tools they want to reuse, performance or privacy requirements.

State assumptions explicitly. If multiple reasonable options exist, present them with tradeoffs and let the user choose — don't pick silently.

### Phase 4 — Write the SPEC.md (leave the chat, commit to a file)

This is the hinge of the whole workflow. The decisions from Phases 0–3 must leave the conversation and become a file. Write `SPEC.md` to the repo using the template in "Output formats" below. The spec describes *what* to build and *why*, not *how to code it*.

After writing it, do NOT paste the full spec back into the chat. Report only: the file path, the title, and one or two open questions if any remain. From this point on, the spec — not this conversation — is the thing everyone refers to.

If the repo doesn't have a `CLAUDE.md`, create a lean one now (project name, purpose, stack, conventions, and a pointer to `SPEC.md` and `PLAN.md`). Keep the most important rules near the top.

### Phase 5 — Decompose into a plan of tasks

Turn the spec into `PLAN.md`: a list of small, numbered tasks, each scoped tightly enough to implement and review in one sitting. For each task give a clear acceptance criterion that is **checkable**, not interpretable — prefer "the test `test_export_csv` passes" or "running `app --help` prints usage" over "exports work well." Concrete criteria are followed far more reliably than vague ones.

Decomposition is the part that genuinely needs the human. Propose the breakdown, then let the user adjust the order, the size, and the priorities before any code is written.

### Phase 6 — Execute, one task at a time, keeping the files alive

Now implement, and only now. For each task:
1. **Use plan mode for anything non-trivial** (roughly: 3+ steps, or any architectural decision). Trivial single-file changes can proceed directly.
2. Implement the task.
3. Verify against its acceptance criterion.
4. **Update the living files.** Whenever a decision changes during implementation, update `SPEC.md` / `PLAN.md` / `CLAUDE.md` to match. This is what keeps planning and code from drifting: the files always describe the code as it actually is. Check tasks off in `PLAN.md` as they complete.
5. Check in with the user at task boundaries — review at the phase gates, not on every individual edit.
6. **Lightweight reflection (cheap).** At the gate, ask yourself one question: did this task expose a *transferable* method worth capturing? Almost always the answer is no — move on, it cost one line of thought. Only if yes, follow "Self-improvement" and raise a separate tagged proposal. Do not turn this into running self-analysis on every step.

When the conversation gets long or you're about to start a meaningfully new piece of work, prefer a **fresh session** over a sprawling one. A fresh session is not a loss, because it re-grounds itself by reading the up-to-date `SPEC.md`, `PLAN.md`, and `CLAUDE.md`. Use `/compact` to condense the current session before picking up the next task without losing the thread, and `/context` to watch token usage so quality doesn't degrade in an overlong session.

### Phase 7 — Retrospective (at project end or a major milestone)

A short, deliberate close-out — this is the main moment learning is allowed to happen, which is what keeps its cost out of every individual task:
1. Make sure `SPEC.md`, `PLAN.md`, and `CLAUDE.md` reflect what was actually built.
2. Run the self-improvement check once for the project as a whole: did anything expose a transferable gap in *method*? If so, raise it as a tagged proposal (see "Self-improvement").
3. Prune `references/lessons-learned.md` if it has grown (see "Pruning the lessons file").

## Keeping planning and code in sync — the core rule

The whole design rests on one idea, so it's worth stating bluntly:

> Never treat a conversation as the source of truth about the project. Conversations freeze and diverge. Files in the repo are the source of truth; conversations are disposable interfaces to those files.

Concretely, that means:
- A decision that isn't written to a file effectively didn't happen — write it down.
- To "consult the planner," you don't revive an old chat; you point any session at the current `SPEC.md` and relevant code.
- Once code exists, planning happens where the code is (i.e. with the codebase available), not in a separate place that can't see it.

## Model selection

Match the model to the cognitive load of the phase, and say which you're suggesting and why:
- **Discussion, architecture, and decomposition (Phases 0–5):** favor the strongest reasoning model available (e.g. an Opus-tier model). These phases are where judgment matters most and a wrong turn is expensive.
- **Executing well-defined tasks (Phase 6):** a fast mid-tier model (e.g. a Sonnet-tier model) is usually the better tradeoff once the task is precisely specified — quicker and cheaper, with the spec doing the heavy lifting.

## Self-improvement (how this skill learns)

This skill can get better over time by capturing genuinely transferable development patterns it discovers while working. Two design goals govern the whole mechanism: it costs almost nothing when nothing is learned, and it never changes the skill's behavior without the user's explicit approval.

### Prerequisite: install globally
Self-improvement only works if lessons accumulate *across* projects. Install this skill at `~/.claude/skills/` (user scope), not inside a single project's `.claude/skills/`. A project-scoped copy resets its memory at every new repository, so the "seen on N projects" signal below can never build up.

### Where things live
- **Stable core** = this `SKILL.md`. It loads every time the skill triggers, so its size is a permanent tax on every future run on every project. Keep it lean; change it rarely.
- **Accumulated lessons** = `references/lessons-learned.md`. This file costs nothing until something reads it. New patterns land *here first*, never directly in the core. At the start of a project's planning, read it; during work, consult it when a relevant situation arises.
- **History** = `CHANGELOG.md`. Every change to the core, and every promotion or non-trivial prune, is logged here with a date — so changes are visible and reversible.

### The methodology filter (what is even a candidate)
Most things learned during a project are NOT skill material. Route by type, and apply this test explicitly before proposing anything:
- A quirk of *this* codebase / API / environment → goes to the project's `CLAUDE.md`. Skill unchanged.
- A mistake made in *this* session, or a genuine one-off → goes nowhere (or `CLAUDE.md` if it will recur on this project). Skill unchanged.
- A **transferable method of developing software** — something that would change how you'd approach a *future, unrelated* project — is the only thing that qualifies as a candidate for the lessons file.

This filter rejects the large majority of candidates. That is the point: it keeps both noise and token cost down by stopping most "lessons" before any expensive drafting happens.

### When reflection happens
Event-driven and lightweight, never continuous (continuous reflection is the expensive, noisy failure mode). Reflect only at task gates in Phase 6 (a one-line check) and at the Phase 7 retrospective. The costly step — reading the skill and drafting an edit — runs only when a candidate clears the filter, and only with the user's go-ahead.

### How a proposal looks
When (and only when) a candidate clears the filter, raise it as a clearly tagged item, separate from the project questions and never applied silently:

```
🔧 SKILL IMPROVEMENT PROPOSAL
Pattern: <the transferable method, in one or two sentences>
Why it's fundamental: <why this changes the approach to future, unrelated projects>
Proposed text: <the exact lines to add to references/lessons-learned.md>
```

If the user approves, append the lesson to `references/lessons-learned.md` and log it in `CHANGELOG.md`. If the user declines, drop it. Never edit `SKILL.md` or the lessons file without an explicit yes.

### Privacy: keep identifying details out of the lessons file
`references/lessons-learned.md` is the one file in this skill that's designed to be shared or published (e.g. if you ever publish your fork of this skill). Never write real project names, client names, account names, repo URLs, or other identifying details into it — describe the pattern itself plus a generic descriptor of the context if useful ("a browser extension", "a data pipeline"), not who it was for. This applies to every lesson and to the "Seen on" counter below. If a lesson is genuinely inseparable from private specifics, it belongs in that project's `CLAUDE.md` instead, not here.

### Lesson format and the promotion counter
Each lesson records how many *distinct* projects it has appeared on, so promotion later is mechanical rather than a matter of taste. Track this as a count only — never list real project names (see "Privacy" above):

```
### <short lesson title>
- Pattern: <the method>
- Seen on: <N> distinct projects
- Status: lesson | promoted
```

When you would add a lesson that already exists, don't duplicate it — just increment the count (dedup on add).

### Promotion to the core
A lesson earns promotion into `SKILL.md` only once it has appeared on **3 or more distinct projects** and is clearly load-bearing for the methodology. Promotion is itself a forked decision: propose it, get approval, move the text into the appropriate phase of the core, mark the lesson `promoted` in the lessons file, and log it in `CHANGELOG.md`. Promote sparingly — every promotion permanently grows the always-loaded core.

### Pruning the lessons file
The lessons file is free until opened, but once opened a bloated file costs context and dilutes signal. During the Phase 7 retrospective, do a quick pass: collapse near-duplicates, delete lessons that proved wrong or obsolete, and keep entries terse. Log any non-trivial pruning in `CHANGELOG.md`.

## Output formats

### SPEC.md

Use this structure. Fill every section; where information is genuinely unknown, write it as an explicit "Open question" rather than inventing an answer.

```markdown
# [Project name] — Specification

## Problem
What real problem this solves, and for whom. (From Phase 0.)

## Why build (vs. existing tools / no software)
The conclusion of the build-vs-not decision, with the alternatives considered. (From Phase 1.)

## What it does
Core user-facing behaviors, as a short list.

## How it should feel to use
The intended UX, described from the user's point of view.

## Out of scope (for now)
What this explicitly will not do in v1.

## Platform & stack
Form factor, language(s), key dependencies, where it runs, how it's distributed.

## Constraints
Fixed requirements: OS, privacy, performance, tools to reuse.

## Acceptance criteria
Checkable conditions that mean "this works."

## Open questions
Anything still undecided.
```

### PLAN.md

```markdown
# [Project name] — Plan

Source of truth: SPEC.md

## Tasks
- [ ] 1. <task> — acceptance: <checkable criterion>
- [ ] 2. <task> — acceptance: <checkable criterion>
- [ ] 3. ...

## Notes / decisions log
- <date> — <decision made during implementation, and why>
```

## Anti-patterns to avoid

- Writing code in Phase 0–3. If you catch yourself reaching for an editor before `SPEC.md` exists, stop.
- Skipping Phase 1. "They clearly want to build it" is not a reason to skip the question of whether they should.
- Letting the spec go stale. An out-of-date `SPEC.md` is worse than none, because it lies. Update it as you go.
- Dumping the full spec or plan into chat. Report the file path and a short summary; the file is the artifact.
- Trying to keep two separate conversations "in sync." Don't. Point both at the same files instead.
- Pouring every lesson into `SKILL.md`. New lessons go to `references/lessons-learned.md`; only patterns proven across 3+ projects earn a place in the always-loaded core.
- Editing the skill silently. Self-improvement is always a tagged proposal the user approves and a `CHANGELOG.md` entry — never an automatic rewrite.
- Reflecting continuously. Reflection is a one-line check at gates and a real pass only at the retrospective; constant self-analysis is the expensive failure mode.
