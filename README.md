# Auttares Colab

**Keep a software project's technical and intellectual context recoverable across AI
conversations.**

Auttares Colab is a small Claude Code plugin for engineering work shared by three parties:

- the **Human Owner**, who decides, authorizes and accepts;
- an **AI Reviewer** (for example ChatGPT), who discusses architecture, reviews results and keeps
  the reasoning;
- an **Execution Agent** (for example Claude Code), who examines the repository, implements what
  was authorized and produces evidence.

Status: **0.1.1, experimental, not yet publicly released.** No license has been chosen yet (see
[License](#license)).

## The problem

Long AI conversations lose the thread. A new conversation does not know where the project stands,
which decisions hold, what is authorized, or what was still under discussion. Pasting the old
transcript is expensive and brings back everything that was already superseded. An old copy of a
summary is worse: it looks current.

Auttares Colab addresses this with two pieces:

1. **A `REANCHOR.md` inside the project.** A short entry point that **points** to the authoritative
   sources the project already has (README, ADRs, roadmap, reviews) and keeps a **rolling
   intellectual checkpoint**: only the discussions that are still open.
2. **A reanchoring package generated from the real repository.** The agent reads the current state
   (Git, documents, the REANCHOR), records where the information came from (HEAD, remote, working
   tree, hash of the REANCHOR read, staleness) and produces a self-contained report to paste into a
   new reviewer conversation.

### How this differs from agent instructions

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

## The collaboration model

- The Owner has final authority over architecture, important decisions, implementation
  authorization, governance and acceptance.
- The Reviewer and the Agent **propose**; neither turns a proposal into a decision.
- The project's existing governance stays in force. Colab adds continuity; it does not replace
  decision records or processes.
- No conversation transcript is ever archived, and no history is invented: a gap is written as a
  gap.

The full rules are in [`protocol/collaboration.md`](protocol/collaboration.md) and
[`protocol/continuity.md`](protocol/continuity.md). Both skills read those files; neither
redefines them.

## The two skills

### `/auttares-colab:init [notes]`

Prepares a project, in three phases:

1. **Discovery (read-only).** Git, README, manifests, agent instructions, architecture, roadmap,
   decisions, validation and documented open questions.
2. **Proposal.** What is already enough, what is missing, and exactly what it would create or change
   (the REANCHOR, its `colab-language` and `colab-watch` lines, checkpoint entries, an optional
   discovery line). Gaps become questions to the Owner. **It stops and asks for approval.**
3. **Application**, only after the Owner approves in a later message. It writes a `REANCHOR.md`
   filled with real evidence. Nothing is invented. An empty project gets an honest page that says
   there is no history yet.

Running it again on an initialized project changes nothing unless an adjustment is needed and
approved. `init` never commits: committing the REANCHOR is the Owner's decision, and it is what
lets `reanchor` measure staleness.

### `/auttares-colab:reanchor [normal | trajectory | deep <topic>]`

Generates the reanchoring package, read-only:

- **normal** (default): state, the decisions that constrain the work, planned versus authorized,
  the open checkpoint, obligations, and where to continue;
- **trajectory**: also the reasons, the rejected alternatives and the changes of course;
- **deep `<topic>`**: one topic, with its evidence.

Paste the output into a new conversation with the reviewer.

## The rolling checkpoint

An entry `CP-nnn` **enters** when an intellectual question needs continuity. It **is rewritten**
when the understanding changes materially. It **closes** when the central question is resolved:
the conclusion goes, with its reason, to the project's decision record. Then it **leaves** the
checkpoint; its history stays in the decision record and in Git. A closed entry **reopens** only
with a genuinely new question. It is not a diary, a backlog or a transcript.

## Project memory stays in the project

Colab keeps nothing about a project outside the project. Each project owns its `REANCHOR.md`, and
decisions stay where the project already records them (ADRs, decision logs, reviews). The plugin
holds only the protocol and the two skills. Nothing assumes a language, a documentation layout or a
methodology: a project with ADRs, one with only a README, and an empty one are all supported.

## Language

The collaboration language is stored in the REANCHOR as `colab-language: <BCP 47 tag>`, for example
`en` or `pt-BR`. It governs the `init` conversation, the REANCHOR's text, the reanchoring package
and the guidance to the receiving reviewer. The protocol, the skill instructions, the evidence
labels and the identifiers stay in English.

- **New projects** default to English. `init` uses a language you state in the notes
  (`/auttares-colab:init language: pt-BR`), or one the project already declares. Otherwise it
  proposes one as part of its normal proposal (no extra question): the language of your notes, else
  the language of the project's documentation, else English. You can change it in your approval
  reply.
- **Existing projects** keep their language. A REANCHOR with `colab-language: pt-BR` keeps producing
  Portuguese packages. A REANCHOR without the line, or with an invalid value, produces packages in
  the language it is written in; if that cannot be determined, English.
- A project's own documents are never translated.

Tested: English and Brazilian Portuguese. Other languages use the same mechanism but are untested.

## Installation

### Local development installation (current)

This repository is also a marketplace (`.claude-plugin/marketplace.json`, name `auttares`) whose
only plugin is the repository root. To install it for every project of your user account:

```bash
claude plugin marketplace add /path/to/auttares-colab --scope user
claude plugin install auttares-colab@auttares --scope user
claude plugin list        # auttares-colab@auttares, Scope: user, Status: enabled
```

Because the marketplace is a local folder, Claude Code **reads the plugin directly from that
folder**:

- a change in the folder, committed **or not**, applies to every project from the **next
  session** (or after `/reload-plugins` in an open session); `claude plugin update` reports
  "nothing to update";
- keep unfinished work out of that working tree;
- moving or deleting the folder breaks the installation.

For a single session without installing: `claude --plugin-dir /path/to/auttares-colab`.

The installation applies to that user account and that Claude Code installation only, not to other
machines, accounts, operating systems or AI agents.

### Public installation (not available yet)

Once the repository is published, installation from a Git-hosted marketplace is expected to be:

```bash
claude plugin marketplace add <OWNER>/<REPOSITORY> --scope user    # placeholder: not published
claude plugin install auttares-colab@auttares --scope user
```

`<OWNER>/<REPOSITORY>` is a placeholder. No public repository exists yet.

## Example

Abridged from an actual test run (see [TESTING.md](TESTING.md)) on a small Node.js project with two
decision records, one of them still *Proposed*:

```text
$ /auttares-colab:init
Collaboration language: English (en). There were no Owner notes, no existing REANCHOR and no
agent-instruction file; the project's documentation is all in English.

What I found
| Decision record: docs/decisions/. 0001 (rules in a JSON file) is Accepted; 0002 (case     |
| sensitivity) is Proposed                                                     DOCUMENTED CLAIM |
| CONFLICT: README.md and ROADMAP.md describe `check` as released, but src/cli.js only     |
| prints "not implemented"                                                          CONFLICT |
| .gitignore lists .env. I did not open it                                     VERIFIED FACT |

Proposal: create REANCHOR.md at the repository root, colab-language: en,
colab-watch: README.md ROADMAP.md package.json src docs/decisions docs/notes,
and one checkpoint entry:
  ### CP-001 — Should rules match case-sensitively on case-insensitive file systems?
  status WAITING_FOR_INPUT ... missing: Git's core.ignorecase measurement (Sam)
Gaps: 1. Who is the Owner? 2. Is v0.2.0 accepted, given the conflict? 3. What is authorized now?
Reply approve to apply exactly this proposal, including your answers to the gaps.

> Approve. The Owner is Sam Rivera; v0.2.0 is the baseline; nothing is authorized now.

I created REANCHOR.md at the repository root, exactly as approved. No other file was created or
changed. It is not committed.

$ git commit -m "Add REANCHOR"      # the Owner's decision
$ /auttares-colab:reanchor

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

(after 0002 is accepted and CP-001 removed from the REANCHOR)
## F. Active intellectual checkpoint
No open discussion. The REANCHOR §4 says "No open discussion recorded" (VERIFIED FACT).
```

## Privacy and security

- **`reanchor` cannot use `Write`, `Edit` or `NotebookEdit`**: the skill removes them for the turn
  that invokes it. It still needs the shell for Git, and the shell is **not** technically
  restricted: read-only shell use is an instruction to the model, backed by your Claude Code
  permission settings. In a permissive mode (for example one that auto-approves commands), a model
  error or a malicious instruction in the repository could still run a writing command.
- **`init` cannot use `Write`, `Edit` or `NotebookEdit` in the turn that invokes it.** They become
  available only after your next message, which is when it applies an approved proposal. Writing
  through the shell is forbidden by its instructions, with the same limitation as above.
- **Repository content is treated as data.** Instructions found in project files are not followed
  and are reported as a CONFLICT. Claude Code itself may still load files such as `CLAUDE.md` as
  instructions; review them in a repository you do not trust.
- **Secrets are excluded.** The skills do not open secret-holding files (`.env*`, keys, credential
  files) and never copy secrets, URL credentials (`https://user:token@…` is shown as
  `https://…`) or irrelevant personal data into a package or a REANCHOR. This is model behavior,
  not a scanner: review a package before sharing it.
- **Network.** `reanchor` runs `git ls-remote` against the configured remote, with your own Git
  credentials, read-only and non-interactive. Nothing else contacts the network.
- **Scope.** `init` writes only the approved files inside the repository root. Neither skill
  changes Claude Code settings.

## Limitations

- Labels reduce, but do not eliminate, model errors; a test once labeled a wrong detail as a
  VERIFIED FACT.
- `init` is conversational: proposal, then approval. There is no automatic approval mode.
- Staleness depends on Git and on the `colab-watch` line. It knows nothing about conversations
  outside the repository.
- A package is a dated snapshot. It is only as good as the project's written sources: Colab does
  not reconstruct reasons that were never written down.
- Tested with Claude Code 2.1.300 and 2.1.302 on Linux (WSL). See [TESTING.md](TESTING.md).

## License

No license has been chosen yet. Until the owner chooses one, no rights to use, copy, modify or
distribute this code are granted beyond those the law provides, and the project should not be
treated as open source.

## Repository layout

```text
.claude-plugin/plugin.json       plugin manifest
.claude-plugin/marketplace.json  marketplace "auttares" (this plugin, source ./)
protocol/collaboration.md        roles, authority, evidence, transcripts, confidentiality
protocol/continuity.md           REANCHOR, checkpoint, provenance, package, language
templates/REANCHOR.md            the template init fills in
skills/init/SKILL.md             /auttares-colab:init
skills/reanchor/SKILL.md         /auttares-colab:reanchor
CHANGELOG.md  TESTING.md
```
