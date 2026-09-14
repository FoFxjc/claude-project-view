---
description: Render a concise, high-level project overview as a private native Artifact — architecture, state, milestones, risks. Not session detail; see /session-view for that.
disable-model-invocation: false
---

# project-view

Produce a **concise, high-level** project overview of the current repository and publish it as a native Artifact. This is the zoomed-out view — for detailed engineering-session detail (timeline, files changed, tests run, decisions, skills/tools used), that's `/session-view`, not this command. Do not build a custom web app, server, renderer, or database — the deliverable is one Artifact call.

## Scope (hard limits)

- Only the **current session** and the **current repository working tree**. No cross-session memory, no persisted history of past project views.
- Do not save, log, or remember the resulting Artifact URL anywhere (no file, no memory, no TODO). Just report the link in chat this turn.
- Do not modify any file belonging to the project being observed. This command is read-only with respect to the target repo.
- Do not invent completion percentages, velocity estimates, or ETAs.
- Never label something `Done`, `Complete`, or `Validated` without citing the concrete evidence for it. If you cannot point to evidence, use a weaker status, or `Unknown` / `Not Observed`.
- **Observe and visualize only — never run new tests, builds, benchmarks, or validation.** Use only evidence that already exists. If nothing supports a claim, mark it `Unknown` / `Not Observed` rather than generating fresh evidence or guessing.
- Do not pre-build abstractions for other tools/platforms (e.g. Codex). This command is Claude Code-specific.
- Any temporary HTML file used to stage the Artifact must be written **outside the observed repo** (e.g. a scratchpad/temp directory), never inside the target project's working tree — even temporarily. The observed repo must end this command with a working tree identical to how it started.
- **This is the concise zoom level.** No file-level diffs, no test/check-by-check detail, no tool-call or skill-usage log, no detailed chronological timeline of this session's actions. If you find yourself listing individual commits, individual files touched, or individual commands run, that belongs in `/session-view` — cut it here and, at most, name the general area.

## Step 1 — Gather evidence

Only run checks relevant to this repo; skip what doesn't apply.

1. **Session context**: reread this conversation for the project's actual purpose and what this session is currently focused on.
2. **Docs**: `README.md`, `CLAUDE.md`, `docs/`, ADR/architecture docs, `CHANGELOG.md`.
3. **Structure**: a directory listing / tree, enough to name major components — not a full inventory.
4. **Git state**: `git log --oneline -20` for a handful of meaningful recent changes; `git status` to know if the tree is clean.
5. **Plans/roadmap**: look for a roadmap doc, milestone list, `TASKS.md`, issue tracker file, or similar checked into the repo. If none exists, the Roadmap section says so plainly — do not infer a roadmap from code shape.

## Step 2 — Classify everything into three buckets

Tag every claim internally as one of:

- **Verified fact** — you read the file, ran the command, or saw the output yourself, this session.
- **Inference** — a reasonable conclusion from evidence, not directly confirmed.
- **Plan / stated intent** — someone said they will do this, or a doc says it's planned; not yet observed as implemented.

Anything you cannot place evidence behind is **Unknown / Not Observed** — a fourth, explicit state, not something to fill in with a plausible guess. Keep all four states visually distinguishable in the final view — don't flatten them into uniform bullet points.

## Step 3 — Design and publish the Artifact

1. Load the `artifact-design` skill before writing any HTML. Load `artifact-diagramming` too if a real architecture diagram is warranted — this should read as a scannable overview, not a wall of text, but also not an over-built dashboard.
2. Build **one self-contained HTML file** covering exactly these sections, using real content gathered above (omit a section entirely if there's nothing honest to put in it — never pad, never invent to fill a section):
   - **Project Purpose** — what this project is for, in a sentence or two.
   - **High-level Architecture / Major Components** — the handful of major pieces and how they relate. A small diagram is welcome; an exhaustive file tree is not.
   - **Current Known State** — a short factual snapshot (e.g. "clean, pushed, v0.1.0 shipped"), not a percentage.
   - **Known Milestones** — meaningful points already reached, each with its evidence (a tag, a commit, a changelog entry).
   - **Current Session Focus** — one or two lines on what this session is actually working on right now, at the level a teammate glancing over your shoulder would want.
   - **Major Risks / Blockers** — real, named obstacles, not speculative worst-cases. Keep to the significant ones.
   - **Roadmap / Next Milestones** — only if there's real evidence for it (a roadmap doc, stated plan, open milestone). If there is none, say `Unknown / Not Observed` — do not synthesize a roadmap from the current codebase.
   - **Recent Meaningful Changes** — a *short* list (a handful of items, not a full git log) of the changes that actually matter to understanding the project right now.
3. **No progress bars for overall completion.** This view does not carry the kind of countable per-item evidence that would justify one (that belongs in `/session-view`, scoped to session work). If tempted to add one here, don't.
4. Design for a **30-second read**: strong visual hierarchy, generous whitespace, nothing that requires scrolling through detail to find the point. The repository/project is the subject of this page — not this session's work process.
5. Write the HTML to a path outside the observed repo, then publish it with the `Artifact` tool (default private visibility). Give it a real title and a one-line description. Do not pin it, do not ask for sharing, unless the user asks.

## Step 4 — Report

In chat, briefly state: the Artifact link, and one honest sentence about coverage gaps. Do not restate the whole view in chat text — the Artifact is the deliverable. If the user actually wants session-level engineering detail, point them at `/session-view` instead of padding this one.
