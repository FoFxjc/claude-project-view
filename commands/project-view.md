---
description: Render this session's understanding of the current repo as a private native Artifact project view.
disable-model-invocation: false
---

# project-view

Produce a single-page, private, interactive **project view** of the current repository, using only what this session actually knows or can directly verify right now, and publish it as a native Artifact. Do not build a custom web app, server, renderer, or database to show it — the deliverable is one Artifact call.

## Scope (hard limits)

- Only the **current session** and the **current repository working tree**. No cross-session memory, no persisted history of past project views.
- Do not save, log, or remember the resulting Artifact URL anywhere (no file, no memory, no TODO). Just report the link in chat this turn.
- Do not modify any file belonging to the project being observed. This command is read-only with respect to the target repo.
- Do not invent completion percentages, velocity estimates, or ETAs.
- Never label something `Done`, `Complete`, or `Validated` without citing the concrete evidence for it (a command output, a test result, a file you read). If you cannot point to evidence, use a weaker status (`In Progress`, `Unverified`, `Claimed but not verified`).
- **Observe and visualize only — never run new tests, builds, benchmarks, or validation.** Use only evidence that already exists: what this session already ran/saw earlier in the conversation, and reports/output already checked into the repo (CI logs, test result files, coverage reports, etc.). If no such evidence exists for a claim, mark it `Unverified` rather than generating fresh evidence to fill the gap.
- Do not pre-build abstractions for other tools/platforms (e.g. Codex). This command is Claude Code-specific.
- Any temporary HTML file used to stage the Artifact must be written **outside the observed repo** (e.g. a scratchpad/temp directory), never inside the target project's working tree — even temporarily. The observed repo must end this command with a working tree identical to how it started.

## Step 1 — Gather evidence

Only run checks that are relevant to this repo; skip what doesn't apply rather than padding the report.

1. **Session context**: reread this conversation for what the user's actual current goal is, decisions already made in this session, and work already done in this session.
2. **Docs**: look for `README.md`, `CLAUDE.md`, `docs/`, `ADR`/architecture docs, `CHANGELOG.md`.
3. **Structure**: get a directory listing / tree of the repo to ground "Architecture" in real layout, not guesses.
4. **Git state**: `git status`, `git log --oneline -20`, `git diff` (staged and unstaged) to see what's actually changed and recently changed.
5. **Plans/tasks**: look for plan files, task lists, TODO files, issue trackers checked into the repo (e.g. `TASKS.md`, `.claude/plans/`, `PLAN.md`, slice/phase files).
6. **Tests/validation evidence**: look only for *existing* evidence — test/CI result files, coverage reports, prior command output already visible earlier in this session's transcript. Do not run tests, builds, or benchmarks yourself to produce new evidence. If no existing evidence covers a claim, mark it `Unverified`.

## Step 2 — Classify everything into three buckets

For every claim you plan to put in the view, tag it internally as one of:

- **Verified fact** — you read the file, ran the command, or saw the output yourself, this session.
- **Inference** — a reasonable conclusion from evidence, but not directly confirmed (e.g. "this file's naming suggests X pattern").
- **Plan / stated intent** — someone said they will do this, or a doc says it's planned; it is not yet observed as implemented.

Keep this distinction visible in the final view (e.g. via labels, icons, or a legend) — do not flatten it into uniform-looking bullet points.

## Step 3 — Design and publish the Artifact

1. Load the `artifact-design` skill before writing any HTML. Load `artifact-diagramming` and `dataviz` too — this view should read visually, not as a wall of text.
2. Build **one self-contained HTML file** covering these sections, using real content gathered above (omit a section entirely if there is nothing honest to put in it — don't pad):
   - **Architecture** — a real structure/module diagram (via `artifact-diagramming`), grounded in the directory listing and docs you read, not a plain nested-bullet file tree.
   - **Current Goal** — what this session's user is actually trying to accomplish right now.
   - **Current State** — a factual snapshot, not a percentage.
   - **Completed** — a status-board column (kanban-style card, not a bullet list) of items with real evidence (commits, passing tests, files that exist and do what's claimed).
   - **In Progress** — a status-board column alongside Completed, for items with visible partial evidence (WIP commits, draft code, open TODOs).
   - **Validation** — what existing evidence (already run this session, or already checked into the repo) actually shows, and its real result; mark anything without such evidence `Unverified`. Do not run anything new to fill this section.
   - **Blockers / Risks** — real obstacles observed (failing tests, missing deps, unresolved decisions), not speculative worst-cases.
   - **Key Decisions** — decisions actually made in this session or documented in the repo, with why.
   - **Next Steps** — a status-board column (same visual family as Completed/In Progress) of concrete, near-term items grounded in the actual gap between current state and stated goal.
   - **Recent Meaningful Changes** — drawn from `git log`/`git diff`, not guessed.
   - Every card and status-board item still carries its fact/inference/plan tag from Step 2 — the visual upgrade must not blur that distinction.
3. **Progress bars — evidence-driven only.** Draw a progress bar or ratio meter only where the repo gives you an objective denominator you can count directly, e.g. checked vs. total boxes in a task/plan file, or pass/fail counts from an existing CI or test-result artifact. Label it with what it actually counts (e.g. "6 / 9 checklist items checked in TASKS.md", "42 / 44 tests passing per last CI run"). If no such countable source exists, do not draw a bar or invent one from a vibe — say `Unverified` instead. Never render a bar for "overall project completion" — there is no honest denominator for that.
4. Write the HTML to a path outside the observed repo (per the scope rule above), then publish it with the `Artifact` tool (default private visibility). Give it a real title and a one-line description. Do not pin it, do not ask for sharing, unless the user asks.

## Step 4 — Report

In chat, briefly state: the Artifact link, and one honest sentence about coverage gaps (e.g. "tests weren't run, so Validation is marked unverified"). Do not restate the whole view in chat text — the Artifact is the deliverable.
