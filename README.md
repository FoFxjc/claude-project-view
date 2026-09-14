# project-view

A two-command Claude Code plugin. Both commands turn the current session's
understanding of the current repository into a private, interactive
project-status page — using Claude Code's native Artifact capability.
Nothing else.

## Two zoom levels

This plugin deliberately splits into two commands instead of one, because
"what is this project" and "what did this session just do" are different
questions with different audiences and different amounts of detail:

- **`/project-view`** — concise **project overview**. Purpose, high-level
  architecture, current known state, milestones, current session focus,
  major risks, and (only with real evidence) a roadmap. Built to be read in
  about 30 seconds. The repo/project is the subject — no file-level diffs,
  no test-by-test detail, no tool-call or skill log, no detailed timeline.
- **`/session-view`** — detailed **engineering execution view** of the
  current session. Goal, status/phase, current work, a real chronological
  timeline, files/areas changed, validation evidence, tests actually run,
  decisions made, failed attempts/corrections, blockers, risks, next
  actions, evidence coverage, and what skills and tools were actually used
  to get the work done. Built for someone who wants to know exactly what
  happened and how.

Run whichever matches the question you're actually asking. They render as
separate Artifacts and don't share state.

## Install

Add the marketplace and install the plugin:

```bash
claude plugin marketplace add FoFxjc/claude-project-view
claude plugin install project-view@project-view
```

## Usage

Inside a Claude Code session, in the repo you want a view of:

```
/project-view
```

or

```
/session-view
```

Both commands:

1. Re-read the session for real context (goal, decisions, work done).
2. Check, as relevant, the repo's docs, directory structure,
   `git status`/`log`/`diff`, any plan/task files, and existing
   test/validation evidence — never running new tests or builds to
   manufacture evidence.
3. Classify every claim as a **verified fact**, an **inference**, a
   **stated plan**, or explicitly **Unknown / Not Observed** — and keep
   that distinction visible in the output.
4. Design and publish one self-contained HTML page via Claude Code's
   native `Artifact` tool.
5. Reply in chat with the Artifact link.

The Artifact is created private by default, per Claude Code's normal
Artifact behavior.

## Scope and non-goals

Both commands are intentionally thin. They do **not**:

- Persist anything across sessions — each run is a fresh look, scoped to
  the current session only.
- Remember or store the Artifact URL anywhere (not in a file, not in
  memory).
- Run their own web server, HTML renderer, database, or dashboard
  framework — page generation and hosting are entirely Claude Code's
  native Artifact tool.
- Modify any file in the project they're reporting on. Both are read-only
  with respect to the target repo.
- Report completion percentages, velocity, or ETAs they can't back with
  evidence.
- Mark anything `Done`/`Validated` without citing what was actually
  checked — and mark anything with no evidence at all `Unknown` /
  `Not Observed` rather than guessing.
- Build in any abstraction for other agent platforms (e.g. Codex). This is
  Claude Code-specific, by design.

If a section has no honest content, the command omits it rather than
padding the page.

## How it's built

- `.claude-plugin/marketplace.json` and `.claude-plugin/plugin.json` — the
  standard Claude Code plugin/marketplace manifests.
- `commands/project-view.md` — the entire behavior of `/project-view`, as a
  single slash-command prompt.
- `commands/session-view.md` — the entire behavior of `/session-view`, as a
  single slash-command prompt.

There is no other code in this repo: each command instructs Claude to use
its own native `artifact-design` (and, where warranted,
`artifact-diagramming` / `dataviz`) skills and the native `Artifact` tool
to design and publish its page.

## License

MIT — see [LICENSE](LICENSE).
