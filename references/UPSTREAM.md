# Upstream

## Source

- **Repository**: https://github.com/mattpocock/skills
- **Commit**: `49dd158d1076134a641b33efb035946536778336` (2026-10-09), plugin version 1.3.1
- **License**: MIT, copyright Matt Pocock. Kept in [`../LICENSE`](../LICENSE).

Techniques (not files) were also taken from https://github.com/obra/superpowers at commit `bb92a77741419a4ab5f06e711a283343f1ada0c3` (MIT, copyright Jesse Vincent): `systematic-debugging` and `verification-before-completion`. They were rewritten in our own words.

## Name mapping

| Skill here | Upstream path | Invocation |
|---|---|---|
| `ask-skills` | `skills/engineering/ask-matt` | user |
| `grill-me` | `skills/productivity/grill-me` | user |
| `grilling` | `skills/productivity/grilling` | model |
| `grill-with-docs` | `skills/engineering/grill-with-docs` | user |
| `domain-modeling` | `skills/engineering/domain-modeling` | model |
| `wait-what` | `skills/productivity/wait-what` | user |
| `codebase-design` | `skills/engineering/codebase-design` | model |
| `diagnosing-bugs` | `skills/engineering/diagnosing-bugs` | model |
| `tdd` | `skills/engineering/tdd` | model |
| `research` | `skills/engineering/research` | model |
| `prototype` | `skills/engineering/prototype` | model |
| `implement` | `skills/engineering/implement` | user |
| `review-my-code` | `skills/engineering/code-review` | model |
| `handoff` | `skills/productivity/handoff` | user |

`domain-modeling` is not one of the 13 originally requested skills. It was added because `grill-with-docs` is only a call to `grilling` plus `domain-modeling`; without it, grill-with-docs would lose its meaning. `prototype` was first dropped, then restored at the user's request as a general-purpose, technology-agnostic skill.

All supporting files of these skills were kept: `PHASE-BOUNDARIES.md`, `ADR-FORMAT.md`, `GLOSSARY-FORMAT.md`, `DEEPENING.md`, `DESIGN-IT-TWICE.md`, `LOGIC.md`, `UI.md`, `scripts/hitl-loop.template.sh`, `tests.md`, `mocking.md`, and every `agents/openai.yaml`.

## Not imported

- **`setup-matt-pocock-skills`**: it configured an issue tracker, triage labels, and the domain-doc layout. This toolkit uses no tracker or triage; the domain-doc reading rules moved into the global `AGENTS.md`, so no setup step is needed.
- **`to-spec`, `to-tickets`, `implement-spec`, `triage`, `wayfinder`**: tracker- and spec-driven flows.
- **`improve-codebase-architecture`**: its exploration step became the review mode of `codebase-design`; its HTML report was dropped; its exploration sub-agent was kept.
- **`retro`, `pr`, `wizard`, `teach`, `to-questionnaire`, `writing-for-agents`**: not needed by the selected skills.
- Upstream repository tooling: `docs/`, `.changeset/`, `.claude-plugin/`, `scripts/`, bucket READMEs. Like upstream, this repo has no separate rules folder or test suite: shared rules live in `AGENTS.md`.

## Customisations

Shared, in the global `AGENTS.md` (which also has a Custom rules section for personal additions): precedence between user, project, and global rules; intent classification; clarification only when material; decision and scope preservation; evidence before completion claims; execution boundaries (read-only Git, nothing pushed to remote hosting, no installs or doc writes unless asked); subagents used where a skill calls for them or where they save context and time.

Per skill:

- **ask-skills**: router rewritten for this collection. No unavailable skills, no tracker, no mandatory flow, recommends without running. `PHASE-BOUNDARIES.md` made harness-neutral (`/clear` or `/new`; subagents only on request).
- **grilling**: sub-agent fact lookup kept (quick reads done directly); only questions that change the outcome; settled decisions are stated, not re-asked; ends with a summary and no code or documents.
- **grill-with-docs**: unchanged mechanism; description now says it proposes doc updates.
- **domain-modeling**: proposes glossary entries and ADRs inline and writes them only when the user agrees; honours project-defined doc locations.
- **wait-what**: glossary optional; explicitly explains without continuing the task.
- **codebase-design**: added a review mode (scoping, friction signals, deletion test, candidates in chat, then stop). Review mode walks the codebase with a sub-agent; design-it-twice keeps its parallel sub-agents.
- **diagnosing-bugs**: upstream name kept (it was briefly renamed `debug`, which was confusing); six phases and feedback-loop discipline kept. Added: diagnose-only requests stop after Phase 4; read errors and recent changes first; trace bad values backward to their source; stop after three failed fixes. `git bisect` handed to the user; the confirmed hypothesis goes in the report instead of a commit message.
- **tdd**: refactor-on-green restored inside the change's scope (upstream had moved it out of the loop); seams are confirmed only when not already obvious or agreed; trivial, non-behavioural changes skip the loop.
- **research**: rewritten around two modes (focused, open-ended). Broad reading goes to a background agent as upstream, focused questions are read directly; answers in chat with citations; files only on request. Primary-source discipline kept.
- **implement**: works from the conversation or a named file instead of tickets; no tracker fetch, auto-review, or commit; reports verification results.
- **review-my-code**: renamed from `code-review` to avoid the built-in review commands of Claude Code (`/code-review`, `/review`) and Codex (`/review`). Three axes (Correctness added to Standards and Spec, which is renamed Scope), each axis a parallel sub-agent as upstream, inline for small diffs. Takes a fixed point, a commit, uncommitted work, or files; no tracker or setup dependency; reports without fixing. Smell baseline kept verbatim.
- **prototype**: both branches, the shareable logic demo, the radically-different-variants rule, and every anti-pattern kept. Added: when to build one (an explicit request builds; authorised experimentation may use one; explanation, research, or discussion only suggests one and waits; never a required step before implementing); plain HTML/CSS/JS is the default environment, a specific runtime only when the question depends on it, and the report says whether the result is a simulation or verified in the target environment; no new dependencies. UI branch gained sub-shape C, a standalone HTML file, for questions that don't depend on the app around them. The capture step (commit to a `prototype/<name>` branch, pointer on the implementation issue, fold into main) became: report the answer in chat, leave the prototype in place, and fold in, branch, or delete only on request.
- **handoff**: output in chat by default, file on request; fixed sections (objective, decisions, completed, remaining, files, next action, suggested skills).

## Unresolved

- **Name collisions**: `code-review` was renamed to `review-my-code` to avoid the built-in review commands. If a skill is shadowed, rename it in your copy or install the set as a namespaced plugin.
- **Codex rules import**: Codex reads skills from `~/.agents/skills` (confirmed in the Codex docs, symlinks supported) but has no confirmed import syntax for `AGENTS.md`, so the global `AGENTS.md` is copied or symlinked to `~/.codex/AGENTS.md`.
- **Not exercised in a live harness**: skills were validated statically (metadata, references, structure), not yet run against Claude Code or Codex.
