---
description: Render a detailed engineering-execution view of the current Claude Code session as a private native Artifact — timeline, evidence, decisions, skills/tools used. Not a project overview; see /project-view for that.
disable-model-invocation: false
---

# session-view

Produce a **detailed engineering-execution view** of what this Claude Code session actually did, and publish it as a native Artifact. This is the zoomed-in view — for a concise, high-level project overview, that's `/project-view`, not this command. Do not build a custom web app, server, renderer, or database — the deliverable is one Artifact call.

## Scope (hard limits)

- Only the **current session** and the **current repository working tree**. No cross-session memory, no persisted history of past session views.
- Do not save, log, or remember the resulting Artifact URL anywhere (no file, no memory, no TODO). Just report the link in chat this turn.
- Do not modify any file belonging to the project being observed. This command is read-only with respect to the target repo.
- Do not invent completion percentages, velocity estimates, or ETAs.
- Never label something `Done`, `Complete`, or `Validated` without citing the concrete evidence for it (a command output, a test result, a file you read). If you cannot point to evidence, use a weaker status (`In Progress`, `Unverified`), or `Unknown` / `Not Observed` if there's nothing to go on at all.
- **Observe and visualize only — never run new tests, builds, benchmarks, or validation.** Use only evidence that already exists: what this session already ran/saw earlier in the conversation, and reports/output already checked into the repo (CI logs, test result files, coverage reports, etc.). If no such evidence exists for a claim, mark it `Unverified` / `Unknown` rather than generating fresh evidence to fill the gap.
- Do not pre-build abstractions for other tools/platforms (e.g. Codex). This command is Claude Code-specific.
- Any temporary HTML file used to stage the Artifact must be written **outside the observed repo** (e.g. a scratchpad/temp directory), never inside the target project's working tree — even temporarily. The observed repo must end this command with a working tree identical to how it started.
- If the actual conversation transcript for the work being described isn't available in current context (e.g. a prior/compacted session, reconstructing from git history alone), say so plainly in the view rather than presenting a reconstructed narrative as witnessed fact.

## Step 1 — Gather evidence

Only run checks relevant to this session; skip what doesn't apply.

1. **Session context**: reread this conversation in full — the actual goal, decisions made, work done, dead ends hit, and corrections along the way.
2. **Git state**: `git status`, `git log --oneline -20` (and more detail — full messages, diffstat — for commits made this session), `git diff` (staged and unstaged) to ground the timeline and changed-areas sections in real changes.
3. **Tests/validation evidence**: look only for *existing* evidence — test/CI result files, coverage reports, prior command output already visible earlier in this session's transcript. Do not run tests, builds, or benchmarks yourself to produce new evidence. If no existing evidence covers a claim, mark it `Unverified`.
4. **Skills used**: scan the transcript for skills actually invoked (via the `Skill` tool, slash commands, or explicit skill loads) that materially shaped the work — not every skill glanced at.
5. **Tools/capabilities used**: scan the transcript for tools/capabilities that were meaningful to how the work got done — e.g. `Artifact`, git operations, browser automation, subagents (`Agent`), a dev-exec/sandbox tool, external integrations. Skip routine, low-signal calls (a single `Read` of one file isn't worth a line item; a subagent that did real independent work is).

## Step 2 — Classify everything into three buckets

For every claim, tag it internally as one of:

- **Verified fact** — you read the file, ran the command, or saw the output yourself, this session.
- **Inference** — a reasonable conclusion from evidence, but not directly confirmed (e.g. "this file's naming suggests X pattern").
- **Plan / stated intent** — someone said they will do this, or a doc says it's planned; it is not yet observed as implemented.

Anything with no evidence behind it at all is **Unknown / Not Observed** — do not fill the gap with a plausible guess. Keep this four-way distinction visible in the final view (labels, icons, a legend) — do not flatten it into uniform-looking bullet points.

## Step 3 — Design and publish the Artifact

1. Load the `artifact-design` skill before writing any HTML. Load `artifact-diagramming` and `dataviz` too — this view should read visually and scan fast, not as a wall of text.
2. Build **one self-contained HTML file** covering these sections, using real content gathered above (omit a section entirely if there's nothing honest to put in it — don't pad):
   - **Session Goal** — what this session's user is actually trying to accomplish.
   - **Status / Current Phase** — a real state word (`ACTIVE`, `BLOCKED`, `READY FOR REVIEW`, `DONE`, `IMPLEMENT`, `VALIDATE`, `REVIEW`, etc.), not a percentage.
   - **Current Work** — what's happening right now, specifically.
   - **Detailed Session Timeline** — chronological, grounded in the transcript and/or git log/commit timestamps: investigation, design decisions, implementation, validation, issues found, corrections, commits/pushes.
   - **Completed This Session** — a status-board column (kanban-style, not a flat bullet list) of items with real evidence.
   - **Files / Areas Changed** — drawn from `git diff`/`git log`, grouped meaningfully rather than a raw file dump if the change set is large.
   - **Validation Evidence** — what existing evidence actually shows, with its real result. Do not run anything new to fill this section.
   - **Tests / Checks Actually Performed** — literally what was run and what it returned, this session; if nothing was run, say so.
   - **Decisions Made** — decisions actually made in this session, with why.
   - **Failed Attempts / Corrections** — when meaningful: approaches tried and abandoned, mistakes caught and fixed. Omit if there weren't any worth noting — don't manufacture drama.
   - **Blockers / Open Questions** — real obstacles or unresolved decisions.
   - **Risks / Uncertainty** — genuine uncertainty in what was done or how it'll hold up, not speculative worst-cases.
   - **Next Actions** — a status-board column (same visual family as Completed) of concrete, near-term items.
   - **Evidence Coverage** — an honest note on what this view could and couldn't verify (e.g. "no test suite exists for this repo"; "transcript for the initial build isn't in current context, reconstructed from git history").
   - **Skills Used** — each skill actually invoked this session that materially shaped the work, and what it did *in this session* (not a generic description of the skill).
   - **Tools / Capabilities Used** — the meaningful tools/capabilities used to get the work done (Artifact, git, browser, subagents, dev-exec, external services, etc.) — not a raw tool-call log.
   - Every card and status-board item still carries its fact/inference/plan/unknown tag from Step 2.
3. **Progress bars — evidence-driven only.** Draw a progress bar or ratio meter only where there's an objective, countable denominator (e.g. checklist items checked vs. total, pass/fail counts from an existing test-result artifact). Label it with what it actually counts. Never render a bar for "overall session completion" or "overall project completion" — there's no honest denominator for that.
4. Write the HTML to a path outside the observed repo, then publish it with the `Artifact` tool (default private visibility). Give it a real title and a one-line description. Do not pin it, do not ask for sharing, unless the user asks.

## Step 4 — Report

In chat, briefly state: the Artifact link, and one honest sentence about coverage gaps (e.g. "no test suite exists, so Tests/Checks is empty"; "transcript for part of this session wasn't available, so that stretch is reconstructed from git history and marked accordingly"). Do not restate the whole view in chat text — the Artifact is the deliverable.
