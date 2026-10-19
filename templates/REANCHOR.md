<!-- auttares-colab:reanchor protocol=0.1 -->
# REANCHOR — {{PROJECT NAME}}

<!-- Template. `init` writes every human-readable line below in the collaboration language
     (colab-language) and removes this comment. The marker, `colab-language`, `colab-watch`,
     `CP-nnn`, the status values and the evidence labels stay as written. -->

```text
Nature:     entry point for resuming this project's context (Auttares Colab, protocol 0.1).
            It decides NOTHING. If this page diverges from a cited source, the source wins and
            the divergence is a defect of this page.
Rules:      no conversation transcripts here; no invented history; reanchoring does not
            authorize implementation. The decision-maker is {{OWNER}}.
Language:   colab-language: {{LANGUAGE TAG}}
```

## 1. Where everything is

| What | Where |
|---|---|
| Purpose and vision | {{path or "nothing recorded"}} |
| Architecture | {{path or "nothing recorded"}} |
| Decisions | {{path of the decision record or "nothing recorded"}} |
| Plan / roadmap | {{path or "nothing recorded"}} |
| Validation and tests | {{how the project validates; documented path or command}} |
| Results and historical evidence | {{paths; `git log`}} |
| Agent instructions | {{CLAUDE.md / AGENTS.md or "none"}} |

## 2. Operational reference

```text
verified on          {{YYYY-MM-DD}}, against commit {{short sha}} ({{branch}})
accepted baseline    {{the last accepted version, tag or commit, or "none yet"}}
in progress          {{the work in progress, or "nothing"}}
next planned         {{the next step, with its source, or "nothing planned"}}
authorization        {{AUTHORIZED by <who, where> | NOT authorized | unknown}}
blockers             {{the blockers, or "none recorded"}}
```

`colab-watch: {{paths whose changes make this snapshot stale}}`

**Staleness.** With `R=$(git log -1 --format=%H -- <this file>)`, run
`git log --oneline "$R"..HEAD -- <colab-watch paths>`. If the output is not empty, or the working
tree has changes in those paths, the snapshot is stale and the sources win. An empty output does
not prove that conversations outside Git are in sync.

**Pending obligations** (these are obligations, not discussions):

- {{obligation}}: {{condition that makes it relevant}} ({{source}}), or "none recorded"

## 3. Current decisions

Decisions live in {{decision record}}. Those that constrain the current work:

- {{ID}}: {{one line}} ({{path}}), or "no decision recorded yet"

## 4. OPEN DISCUSSIONS — ROLLING CHECKPOINT

Only discussions that still need reasoning. Not a backlog, a timeline or a transcript.

{{CP-nnn entries in the protocol format, or: "No open discussion recorded."}}

## 5. How to maintain

- An unresolved question that needs continuity **enters** with a stable `CP-nnn` ID.
- It **is updated** by rewriting the entry when the understanding changes materially.
- It **closes** when the central question is resolved: the durable conclusion goes, with its
  reason, to {{decision record}}, and the entry **leaves** this page.
- It **reopens** only with a new question, citing the earlier conclusion.
- Update at transitions, not on every message.
- The Owner confirms decisions. The reviewer proposes. The agent applies them within an authorized
  mission.

Full protocol: Auttares Colab, `protocol/continuity.md` (version 0.1).
