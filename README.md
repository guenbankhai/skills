# Skills

A small, curated set of Agent Skills for Claude Code and OpenAI Codex, adapted from [Matt Pocock's skills](https://github.com/mattpocock/skills). It improves how a developer and a coding agent work together: learning, research, clarifying requirements, design, implementation, debugging, review, and handing context between sessions.

It is not an agent framework. There is no mandatory workflow: each skill is a tool you reach for when the situation calls for it.

## Design philosophy

1. **Intent fidelity**: do what was actually asked, in the mode it was asked (explain, research, design, build, review).
2. **Decision continuity**: agreed decisions stay agreed; conflicts are reported, not silently resolved.
3. **Scope control**: no silent expansion of the work.
4. **Resource efficiency**: no unnecessary tool calls, context, documents, or subagents.
5. **Technical correctness**: research and verify when it matters; claim success only with evidence.
6. **Flexible workflows**: skills compose; none forces a multi-stage process.

## Skills

**User-invoked**: only you can start these, by typing the command.

| Command | What it does |
|---|---|
| `/ask-skills` | Recommends which skill or flow fits your situation. |
| `/grill-me` | Relentless interview to sharpen a plan or design. Stateless. |
| `/grill-with-docs` | The same interview, checked against the project's glossary and ADRs, proposing doc updates you approve. |
| `/wait-what` | The last message didn't land: re-pitch it plainly, without moving on. |
| `/implement` | Build agreed work, with proportionate verification. No commits. |
| `/handoff` | Compact the conversation into a short handoff (in chat; a file only on request). |

**Model-invoked**: the agent may reach for these on its own when they fit, and you can call them too.

| Command | What it does |
|---|---|
| `/grilling` | The interview primitive: rounds of questions over a decision tree, with recommended answers. |
| `/domain-modeling` | Sharpen the project's domain language; proposes `GLOSSARY.md` entries and ADRs, writes them when you agree. |
| `/codebase-design` | Deep-module vocabulary, an architecture review mode, and design-it-twice for interfaces. Recommends; refactors only on request. |
| `/diagnosing-bugs` | Feedback-loop-first diagnosis of hard bugs and regressions; root cause before any fix. |
| `/tdd` | Red → green → refactor at agreed seams, when tests fit the change. |
| `/research` | Open-ended or source-focused research against primary sources, answered in chat with citations. |
| `/prototype` | Throwaway code that answers one design question: a clickable logic demo or radically different UI variants. |
| `/review-my-code` | Correctness, scope, and standards review of a diff, reported per axis. Fixes nothing unless asked. |

## How to use them

Start from what you need. Not sure which skill fits? Ask `/ask-skills`. A common path when building something:

1. `/grill-with-docs` or `/grill-me`, only when the idea is still fuzzy.
2. `/implement` (driving `/tdd` where it fits).
3. `/review-my-code`, when you want a review.

`/diagnosing-bugs`, `/research`, `/prototype`, `/codebase-design`, and `/wait-what` stand alone. `/handoff` is for moving work to another harness, directory, or person.

## Customising

Like the upstream skills, everything is configured in `AGENTS.md` files. There are two levels:

- **Toolkit**: [`AGENTS.md`](AGENTS.md) in this repo holds the shared rules (intent, clarification, decisions and scope, research, verification, execution boundaries). Copy them into each project's `AGENTS.md` (see Installation); the skills assume they are loaded and do not repeat them. Add your own rules there too, for example "Reply in Vietnamese."
- **Per project**: each project configures itself in its own `AGENTS.md` / `CLAUDE.md`: overview, commands, constraints, and pointers to deeper docs. The toolkit imposes no technology or structure. If the project keeps a domain glossary or ADRs, the skills read them from `GLOSSARY.md` and `docs/adr/`, or wherever the project's `AGENTS.md` says.

**Precedence**: your explicit instructions in the session, then the project's rules, then the global rules. Project rules may add constraints or explicitly grant permissions; they never silently override a decision you made.

Execution boundaries by default: Git is read-only, nothing goes to remote hosting (no push, pull requests, or issues on GitHub, GitLab, or similar), no dependency installs, no documentation writes, no follow-on tasks. Subagents are used where a skill calls for them or where they save context and time (broad reading, parallel review); small tasks stay inline.

## Installation

Skills are installed per project: copy them into the project's skill folders, where Claude Code and Codex look for them. Nothing global is changed.

| Agent | Skill folder in the project | Call a skill with |
|---|---|---|
| Claude Code | `.claude/skills/` | `/name` |
| Codex | `.agents/skills/` | `$name` (or pick it from `/skills`) |

**1. Clone this repo once**, anywhere outside your project:

```bash
git clone https://github.com/guenbankhai/skills ~/myfolder
```

**2. Copy the skills into your project.** Run from the project root.

macOS / Linux:

```bash
mkdir -p .claude/skills .agents/skills
cp -R ~/myfolder/skills/* .claude/skills/
cp -R ~/myfolder/skills/* .agents/skills/
```

Windows (PowerShell):

```powershell
New-Item -ItemType Directory -Force .claude\skills, .agents\skills | Out-Null
Copy-Item ~\myfolder\skills\* .claude\skills -Recurse -Force
Copy-Item ~\myfolder\skills\* .agents\skills -Recurse -Force
```

Using only one agent? Copy into that agent's folder only.

**3. Add the shared rules.** The skills assume the rules in this repo's [`AGENTS.md`](AGENTS.md) are loaded.

- **Codex** reads the project's `AGENTS.md`. If the project has none, copy this repo's `AGENTS.md` into the project root. If it has one, paste this repo's rules into it.
- **Claude Code** reads `AGENTS.md` by itself only when the project has no `CLAUDE.md`. If the project has a `CLAUDE.md`, add this line to it:

  ```
  @AGENTS.md
  ```

**4. Start (or restart) the agent in the project** and type `/` in Claude Code or `$` in Codex to see the skills.

**Updating**: `git pull` in `~/myfolder`, then repeat step 2. Same-name skill folders are overwritten; your other skills are not touched. If the rules changed, update the copy in your `AGENTS.md` too.

`/review-my-code` is named to avoid the built-in review commands of Claude Code (`/code-review`, `/review`) and Codex (`/review`). If a harness built-in shadows another skill here, rename that skill's folder and `name:` in your copy, or ship the set as a namespaced plugin when publishing.

## Maintaining this repository

- Each skill lives in `skills/<name>/`: a `SKILL.md` whose `name:` matches the folder, supporting files beside it, and `agents/openai.yaml` for Codex UI metadata.
- A skill is either **user-invoked** (`disable-model-invocation: true` in `SKILL.md` and `policy.allow_implicit_invocation: false` in `agents/openai.yaml`) or **model-invoked** (neither). Keep the two files in sync.
- Skills reach each other with an explicit instruction to call the Skill tool with a skill's name. Only model-invoked skills can be reached that way.
- Shared behaviour belongs in `AGENTS.md`; a skill holds only its specialised behaviour.
- When a skill is added, removed, renamed, or changes how it fits the flows, update `skills/ask-skills/SKILL.md`, the skills table above, and `references/UPSTREAM.md`.

## Credits

Adapted from [mattpocock/skills](https://github.com/mattpocock/skills) (MIT), with a few debugging and verification techniques from [obra/superpowers](https://github.com/obra/superpowers). See [`references/UPSTREAM.md`](references/UPSTREAM.md) for the source version, name mapping, and customisations.
