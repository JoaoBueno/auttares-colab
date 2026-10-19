# Auttares Colab

**A continuity framework for collaborative software engineering with AI agents.**

Auttares Colab helps a human owner, an AI reviewer and an execution agent preserve engineering
decisions, recover project context and resume work across conversations and sessions. It is a
small [Claude Code](https://code.claude.com) plugin with two skills.

Its purpose is not to save conversation summaries. It preserves **verified project context**, the
**rationale of decisions**, **authorization boundaries**, and the **intellectual discussions that
are genuinely still open**.

> **Status: 0.1.1, experimental.** Tested on Linux with Claude Code 2.1.300–2.1.303. Expect
> rough edges and changes. See [Limitations](#limitations).

## The problem

Long AI conversations lose the thread. A new conversation does not know where the project stands,
which decisions hold, what is authorized, or what was still being discussed. Pasting an old
transcript is expensive and brings back everything that was already superseded. An old copy of a
summary is worse: it looks current.

Auttares Colab addresses this with two pieces:

1. **A `REANCHOR.md` inside the project.** A short entry point that **points** to the
   authoritative sources the project already has (README, decision records, roadmap, reviews) and
   keeps a **rolling intellectual checkpoint**: only the discussions that are still open.
2. **A reanchoring package generated from the real repository.** The agent reads the current
   state (Git, documents, the REANCHOR), records where each fact came from (HEAD, remote, working
   tree, hash of the REANCHOR it read, staleness), and produces a self-contained report that you
   paste into a new conversation with your reviewer.

### Continuity is not agent instructions

Files such as `CLAUDE.md` or `AGENTS.md` tell an agent **how to work**. Auttares Colab preserves
**where the project is and why**, for a reviewer that has **no** access to the repository, with
explicit evidence labels:

| Label | Meaning |
|---|---|
| VERIFIED FACT | checked now, directly |
| DOCUMENTED CLAIM | written in a project source, not independently verified |
| INFERENCE | derived from facts and documents |
| OPEN QUESTION | not answered by the sources |
| CONFLICT | two sources, or a source and a verified fact, disagree |

## Collaboration model

| Role | Responsibility |
|---|---|
| **Human Owner** | decides architecture and governance, authorizes implementation, accepts work. Holds final authority |
| **AI Reviewer** (for example a chat assistant) | discusses architecture, reviews results, keeps the reasoning, proposes opening and closing discussions |
| **Execution Agent** (for example Claude Code) | examines the repository, implements what was authorized, produces verified evidence |

Authorization stays human: the reviewer and the agent **propose**, and neither turns a proposal
into a decision. A reanchoring package never authorizes work. The project's existing governance
stays in force; Colab adds continuity and does not replace decision records or processes.

The full rules are in [`protocol/collaboration.md`](protocol/collaboration.md) and
[`protocol/continuity.md`](protocol/continuity.md). Both skills read those files; neither
redefines them.

## Features

### `/auttares-colab:init [notes]`

Prepares a project, in three phases:

1. **Discovery (read-only).** Git, README, manifests, agent instructions, architecture, roadmap,
   decisions, validation and documented open questions.
2. **Proposal.** What is already enough, what is missing, and exactly what it would create or
   change: the REANCHOR, its `colab-language` and `colab-watch` lines, checkpoint entries, an
   optional discovery line. Gaps become questions to you. **It stops and asks for approval.**
3. **Application**, only after you approve in a later message. It writes a `REANCHOR.md` filled
   with real evidence. Nothing is invented; a gap is written as a gap.

Running it again on an initialized project proposes only the adjustments that are needed. `init`
never commits: committing the REANCHOR is your decision, and it is what lets `reanchor` measure
staleness.

### `/auttares-colab:reanchor [normal | trajectory | deep <topic>]`

Generates the reanchoring package, read-only, in one of three modes:

| Mode | Contains |
|---|---|
| **NORMAL** (default) | state, the decisions that constrain the current work, planned versus authorized, the open checkpoint, obligations, and where to continue |
| **TRAJECTORY** | NORMAL plus the reasons, the rejected alternatives and the changes of course |
| **DEEP `<topic>`** | one topic, with its evidence, and what was and was not read |

Every package starts with its provenance: repository, branch, HEAD, the cached and the actual
remote state, uncommitted changes, the commit and sha256 of the REANCHOR read, and whether the
snapshot is CURRENT, STALE or UNVERIFIABLE.

## The rolling intellectual checkpoint

The checkpoint is the project's current intellectual working set, not a diary, a backlog or a
transcript.

- **Enter.** A genuinely unresolved question that needs future reasoning gets a stable `CP-nnn`
  entry: the question, the current understanding, the documented alternatives, what is missing,
  and what would close it.
- **Update.** When the understanding changes materially, the entry is **rewritten**, not appended
  to. The checkpoint holds the current understanding, not the history of formulations.
- **Close.** When the central question is resolved and the owner accepts the outcome, the durable
  conclusion goes, with its reasons and rejected alternatives, to the project's decision record.
- **Leave.** The closed entry is removed from the checkpoint. Its conclusion stays in the decision
  record and its history in Git, so nothing is lost, and the working set stays small.
- **Reopen.** Only with a genuinely new question, which cites the earlier conclusion.

This is why raw chat transcripts are not needed: what deserves to survive a conversation is the
reviewed decision, its reasons and what is still open, and those live in the project.

## Installation

Requires [Claude Code](https://code.claude.com) and `git`.

From your shell:

```bash
claude plugin marketplace add JoaoBueno/auttares-colab
claude plugin install auttares-colab@auttares --scope user
```

Or inside a Claude Code session:

```text
/plugin marketplace add JoaoBueno/auttares-colab
/plugin install auttares-colab@auttares
```

`auttares` is the marketplace name declared in this repository; `auttares-colab` is the plugin.
Check the result with `claude plugin list` (it shows `auttares-colab@auttares`, enabled). Start a
new session, or run `/reload-plugins` in an open one, to use the skills.

- **Scope.** `--scope user` makes the plugin available in every project of your user account in
  that Claude Code installation. It is not installed for other operating-system accounts, other
  machines, or other AI agents.
- **No GitHub SSH key?** Claude Code tries SSH first. On a machine without a GitHub SSH key, set
  `CLAUDE_CODE_PLUGIN_PREFER_HTTPS=1` so it clones over HTTPS; the repository is public, so no
  GitHub account is needed.

### Updates

An installation from GitHub is a cached copy of a released version; it does not follow every
commit. A new version reaches you when you update:

```bash
claude plugin marketplace update auttares
claude plugin update auttares-colab@auttares
```

or when you enable auto-update for the `auttares` marketplace under **Marketplaces** in `/plugin`
(off by default). Restart the session, or run `/reload-plugins`, after an update.

### Development installation

To work on the framework itself, register a local clone instead. Claude Code then reads the plugin
**directly from that folder**, so every edit applies at the next session, with no version bump:

```bash
git clone https://github.com/JoaoBueno/auttares-colab.git
claude plugin marketplace add ./auttares-colab --scope user
claude plugin install auttares-colab@auttares --scope user
```

A user can register only one marketplace named `auttares` at a time: use either the GitHub one or
a local clone. For a single session without installing, use
`claude --plugin-dir ./auttares-colab`.

## Quick start

Start in a project repository (here, a fresh one):

```bash
mkdir my-project && cd my-project && git init
echo "# my-project: a CLI that converts CSV to JSON" > README.md
git add README.md && git commit -m "Initial commit"
claude
```

1. **Initialize.** In the session, run:

   ```text
   /auttares-colab:init
   ```

   It reads the project and answers with a proposal: what it found, the REANCHOR it would
   create, the collaboration language, and questions about what the repository cannot tell it
   (who decides, what the accepted baseline is, what is authorized now). Nothing is written yet.

2. **Review and approve.** Answer the questions and approve in your next message, for example:

   ```text
   Approve. The owner is Dana. There is no accepted baseline yet. Nothing is authorized.
   ```

   `init` writes `REANCHOR.md`, filled with what it found and your answers, and reports what is
   still a gap.

3. **Commit the REANCHOR** (your decision):

   ```bash
   git add REANCHOR.md && git commit -m "Add REANCHOR"
   ```

4. **Generate a handoff** whenever you move to a new conversation:

   ```text
   /auttares-colab:reanchor
   ```

5. **Transfer it.** Copy the whole package into a new conversation with your AI reviewer. Its
   section J tells the reviewer how to use it: as a dated, repository-anchored report, never as
   authorization.

An abridged package from a real test run, on a small project with one accepted and one proposed
decision:

```text
# Reanchoring package — pathlint
## A. Identity and provenance
- Language: en, from the REANCHOR's colab-language line (VERIFIED FACT).
- Branch / HEAD: main at 7d2562bc... (VERIFIED FACT).  Working tree: clean.
- Staleness: CURRENT. git log 7d2562b..HEAD over the colab-watch paths printed nothing.
...
## F. Active intellectual checkpoint
### CP-001 — Should rules match case-sensitively on case-insensitive file systems?
- status: WAITING_FOR_INPUT ... closes when: the Owner accepts or rejects 0002, with the reason.
...
## K. Where to continue
- Next planned step: 0.3, the `fix` command. It is not authorized and is blocked on decision 0002.
- Authorized now: nothing.
```

After the decision was accepted and CP-001 removed from the REANCHOR, the next package reported
"No open discussion" in section F, with the accepted decision, its evidence and its rejected
alternatives in section D.

## Languages

The protocol and the skill instructions are in English. The collaboration itself can use another
language: the REANCHOR stores it as `colab-language: <BCP 47 tag>`, for example `en` or `pt-BR`,
and it governs the `init` conversation, the REANCHOR's text, the package and the guidance to the
reviewer. Evidence labels and identifiers stay in English.

- **New projects** default to English. `init` uses a language you state in the notes
  (`/auttares-colab:init language: pt-BR`) or one the project already declares. Otherwise it
  proposes one inside its normal proposal: the language of your notes, else of the project's
  documentation, else English. You can change it in your approval.
- **Existing REANCHORs** keep their language. Without a valid `colab-language` line, the package
  uses the language the REANCHOR is written in, then English.
- Your project's own documents are never translated.

Tested: **English** and **Brazilian Portuguese** (`pt-BR`). Other languages use the same mechanism
but have not been tested.

## Privacy and security

Auttares Colab runs inside your own Claude Code environment, with your permissions and your model
provider. It has no server and sends nothing anywhere itself.

- **A package contains project information.** Review it before you share it, as you would any
  document about your project.
- **Repository content is treated as evidence, not as instructions.** Directives found in project
  files are not followed and are reported as a CONFLICT. Claude Code itself may still load files
  such as `CLAUDE.md` as instructions: review them in repositories you do not trust.
- **Secrets are excluded by rule.** The skills do not open secret-holding files (`.env*`, keys,
  credential files), open configuration files only when needed, strip credentials from remote URLs
  in the command itself, and never copy secrets into a package or a REANCHOR. This is model
  behavior, not a secret scanner. Whatever a skill reads is part of the model's context.
- **Read-only is a skill-level restriction, not an operating-system sandbox.** `reanchor` cannot
  use the file-editing tools, and `init` cannot use them in the turn that produces its proposal.
  Both still need the shell for Git, which is restricted only by instruction and by your Claude
  Code permission settings. In a mode that auto-approves shell commands, a model error or an
  injected instruction could still write.
- **Network.** `reanchor` runs `git ls-remote` against the project's configured remote, with your
  own Git credentials, read-only and non-interactively. Nothing else contacts the network.
- **Scope.** `init` writes only the files you approved, inside the repository. Neither skill
  changes Claude Code settings.

## Limitations

- **No perfect memory.** A package is a dated snapshot, as good as the project's written sources.
  Colab does not reconstruct reasons that were never written down.
- **No access to unsynchronized conversations.** What exists only in a chat is not recovered.
  Consolidate what matters into the project at transitions.
- **No guarantee that a document is current.** Staleness detection covers committed changes in
  the watched paths; a document outside them, or one that was never updated, can still be wrong.
  Sources win over summaries, and divergences are reported, not resolved.
- **No automatic authorization.** Reanchoring never authorizes changes; the owner does.
- **Model errors remain possible.** Evidence labels reduce, but do not eliminate, mistakes: a test
  once labeled a wrong detail as a VERIFIED FACT.
- **Compatibility.** Built and tested for Claude Code on Linux. The package itself is plain text
  that any reviewer can read, but other agents, other operating systems and interactive-only
  flows have not been tested. See [TESTING.md](TESTING.md).

## Project status

Version 0.1.1 is an **experimental** pre-release. It is not production-proven. Auttares Colab is
an independent project: it is not affiliated with, sponsored or endorsed by Anthropic or OpenAI.
Claude and Claude Code are trademarks of Anthropic; ChatGPT is a trademark of OpenAI.

See [CHANGELOG.md](CHANGELOG.md) for changes and [TESTING.md](TESTING.md) for what was tested.

## Repository layout

```text
.claude-plugin/plugin.json       plugin manifest (name, version, license)
.claude-plugin/marketplace.json  marketplace "auttares" (this plugin, source ./)
protocol/collaboration.md        roles, authority, evidence, transcripts, confidentiality
protocol/continuity.md           REANCHOR, checkpoint, provenance, package, language
templates/REANCHOR.md            the template init fills in
skills/init/SKILL.md             /auttares-colab:init
skills/reanchor/SKILL.md         /auttares-colab:reanchor
```

## License

Copyright 2026 JoaoBueno.

Licensed under the Apache License, Version 2.0 (`Apache-2.0`). See [LICENSE](LICENSE).
