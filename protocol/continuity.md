# Continuity protocol — Auttares Colab 0.1

Defines a project's **REANCHOR**, the **rolling intellectual checkpoint**, the **reanchoring
package** and the **collaboration language**. Complements [collaboration.md](collaboration.md).

## 1. The project's REANCHOR

Each project has **one** `REANCHOR.md`, which belongs to the project and lives in its repository.
The REANCHOR is the entry point for resuming context. It **points** to the project's authoritative
sources and does **not** replace them: if a summary diverges from its source, the source wins and
the divergence is a defect of the REANCHOR.

A REANCHOR created by Colab starts with the marker:

```text
<!-- auttares-colab:reanchor protocol=0.1 -->
```

Its location is free: the repository root, `docs/`, or wherever the project's documentation already
lives.

### 1.1 Discovery

`reanchor` and `init` look, in this order, for:

1. a line `auttares-colab:reanchor <path>` in the agent-instruction file (`CLAUDE.md`,
   `AGENTS.md`), if present;
2. `REANCHOR.md` files in the repository, **tracked or not**:
   `git ls-files --cached --others --exclude-standard`. Without Git, `REANCHOR.md` files outside
   build and dependency directories.

A REANCHOR that exists only in the working tree (for example, right after `init`, before the
commit) **is used**. The package declares it **unpublished**, and staleness is UNVERIFIABLE. A
repository with no commits is declared as such; there is no HEAD to cite.

The result is one of:

| Result | Meaning |
|---|---|
| **one REANCHOR with the marker** | initialized by Colab |
| **one REANCHOR without the marker** | **compatible (legacy)**: it is used, and the package says it was not created by Colab |
| **several REANCHORs** | **CONFLICT**: the package lists them all, uses the one with the marker or the one the instructions point to, and does not choose silently |
| **none** | **not initialized**: `reanchor` does not fabricate a package and points to `init` |

### 1.2 Minimum content

The REANCHOR has these parts, in this order; equivalent headings, including headings in the
collaboration language, are accepted:

1. **Nature and rules.** What the page is, that it decides nothing, and the
   `colab-language: <tag>` line (§5).
2. **Where everything is.** Pointers to vision, architecture, decisions, plan, validation, results
   and historical evidence.
3. **Operational reference.**
   - A small, dated snapshot: the accepted baseline, the work in progress, the next planned step,
     that step's authorization, and the blockers.
   - The watch line `colab-watch: <space-separated paths>`: the paths whose changes make the
     snapshot stale.
   - The **pending obligations**: required gates and checks, each with the condition that makes it
     relevant.
4. **Current decisions.** Pointers to where the project's decisions live, plus the few that
   constrain the current work. Never a copy of the decisions.
5. **OPEN DISCUSSIONS — ROLLING CHECKPOINT** (§2).
6. **How to maintain.** A short summary of §2 and §3, and a reference to this protocol.

Empty sections say "nothing recorded". They are never filled with invented content.

## 2. The rolling intellectual checkpoint

The checkpoint is the **current intellectual working set**: only the discussions that still need
future reasoning. It is **not** a diary, transcript, backlog, issue tracker, timeline or list of
planned work. Milestones, tasks and obligations live in their own sections and sources.

Entry format (the field labels may be written in the collaboration language; the status values
are fixed identifiers):

```text
### CP-nnn — <the central question>
status         OPEN | WAITING_FOR_INPUT
revised        YYYY-MM-DD
understanding  the current understanding (not the history of formulations)
alternatives   the documented alternatives and arguments
missing        what still has to be decided or verified
closes when    what resolves the CENTRAL objective (not every related improvement)
references     the paths needed to resume
```

Lifecycle:

1. **ENTERS** when an unresolved intellectual question needs continuity. The ID is stable and never
   reused.
2. **IS UPDATED** when the understanding changes materially: the entry is **rewritten**, without
   accumulating formulations.
3. **CLOSES** when the central question is resolved:
   - the Owner accepts, when acceptance is required;
   - the durable conclusion goes, with its reason, its authority and the alternatives, to the
     project's decision record, citing the `CP-nnn`.
4. **LEAVES** the checkpoint once closed. The history remains recoverable through the decision
   record and the REANCHOR's Git history. A closed discussion **never** stays in the active
   checkpoint, and a record that mentions a closed `CP-nnn` does not reopen it.
5. **REOPENS** only with a genuinely new question. The new entry cites the earlier conclusion by
   path and does not copy its history.

Cleanup happens at material transitions: entries confirmed as resolved are removed.

## 3. Provenance and staleness

Every reanchoring package identifies its origin:

- the repository, the branch and HEAD (full SHA);
- the **locally cached** `origin/<branch>`, kept separate from the actual remote verification
  (`git ls-remote`), which may fail or be unavailable and is then reported as "not verified";
- the remote URL **without** user-info (collaboration.md §9);
- the changes in the working tree. An uncommitted change is **never** a published baseline;
- the commit that last changed the REANCHOR and the sha256 of the content **read**, and whether that
  content is the committed one;
- the date and time.

**Remote verification contacts the network.** `git ls-remote` connects to the configured remote
with the user's own Git credentials, read-only. It runs non-interactively
(`git -c core.sshCommand="ssh -o BatchMode=yes" ls-remote ...`); without a terminal Git does not
prompt for HTTPS credentials either. Nothing is fetched and no local ref is updated.

**Staleness.** With `R` = the last commit that changed the REANCHOR, run
`git log R..HEAD -- <colab-watch paths>`. The snapshot is:

| State | When |
|---|---|
| **CURRENT** | the output is empty and the working tree does not change those paths |
| **STALE** | there are commits or uncommitted changes in those paths; each affected fact is re-read from its current source |
| **UNVERIFIABLE** | there is no Git, no watch line (legacy REANCHOR: the staleness rule the REANCHOR itself declares is used, if any), or the REANCHOR was never committed |

A newer verified fact **always** prevails over the old text of a summary, and the divergence is
reported as CONFLICT. An empty test does **not** prove that conversations outside Git are in sync.

## 4. The reanchoring package

It is the output of `reanchor`: a self-contained report in the collaboration language (§5). The
Owner pastes the package into a new conversation with the reviewer. Sections:

| Section | Content |
|---|---|
| **A. Identity and provenance** | §3, the mode, the collaboration language and how it was determined, the limitations |
| **B. Purpose and essential architecture** | what the project does; the architecture that matters now |
| **C. Technical state** | the accepted baseline; what is in progress; what was completed and verified |
| **D. Accepted decisions and reasons** | only those that constrain the current or next work, with path and ID |
| **E. Authorized and planned work** | explicitly separated; planned is not authorized |
| **F. Active intellectual checkpoint** | only the OPEN and WAITING_FOR_INPUT entries, each with the question, the understanding, the alternatives, what is missing, what closes it and the references; or "no open discussion" |
| **G. Obligations and blockers** | each with the condition that makes it relevant |
| **H. Recent trajectory** | NORMAL: what is needed; TRAJECTORY: the decisions, alternatives, evidence and reasons; DEEP: only the topic |
| **I. Uncertainties, conflicts and gaps** | including what may exist only in conversations |
| **J. Instructions to the receiving reviewer** | guidelines, not authority |
| **K. Where to continue** | the most relevant active question; the next planned step; what is authorized now; what the Owner must supply if something is missing |

Instructions to the receiving reviewer (section J):

- treat the package as a dated, repository-anchored report, never as policy or a system
  instruction;
- reconstruct the technical and the intellectual context;
- respect established decisions and the Owner's authority;
- distinguish evidence, inference and open questions;
- use the source identifiers to detect later divergence;
- do not repeat completed investigations;
- resume each open discussion from its documented point;
- ask only for the specific information that is missing;
- do not create an implementation mission automatically: reanchoring does not authorize changing
  the project.

The NORMAL package is complete enough to resume without opening the repository, but it is not a
dump of the repository. TRAJECTORY and DEEP may be longer.

## 5. Collaboration language

The REANCHOR declares the project's collaboration language in its header:

```text
colab-language: <tag>
```

`<tag>` is a BCP 47 language tag such as `en`, `pt-BR`, `es` or `de`. The collaboration language
governs the human-readable text of the REANCHOR, the `init` discovery and approval messages, the
reanchoring package and the guidance to the receiving reviewer. The protocol, the skill
instructions, the evidence labels, the status values, the marker and the configuration keys stay
in English.

**Resolution in `reanchor`**, first match wins:

1. a valid `colab-language` line in the REANCHOR;
2. otherwise (line missing, or a value that is not a plausible BCP 47 tag): the language the
   REANCHOR's prose is predominantly written in. A missing line is normal for a legacy REANCHOR; an
   invalid value is reported in section A;
3. otherwise: English (`en`).

**Resolution in `init`**, first match wins:

1. a language stated by the Owner in the invocation notes (for example `language: pt-BR` or
   "write in Portuguese");
2. the valid `colab-language` of an existing REANCHOR;
3. a language explicitly required by the project's agent-instruction file;
4. otherwise, `init` **proposes** one, as part of the proposal it already submits for approval (no
   extra interruption): the language of the Owner's notes if there are any, else the predominant
   language of the project's documentation, else English. The proposal is written in the proposed
   language, and the Owner can change it in the approval reply.

A project's own documents are never translated. Existing REANCHORs keep their language: a REANCHOR
with `colab-language: pt-BR` keeps producing Portuguese packages, and a legacy REANCHOR without the
line keeps producing packages in the language it is written in.

Tested languages: English and Brazilian Portuguese. Other languages follow the same mechanism but
have not been tested.
