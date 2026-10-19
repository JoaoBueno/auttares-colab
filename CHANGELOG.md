# Changelog

## 0.1.1 — unreleased (local)

- **English-first.** The protocol, both skills, the REANCHOR template, the README, this changelog
  and the test record are now in English.
- **Collaboration language** (`continuity.md` §5). `colab-language: <BCP 47 tag>` in the
  REANCHOR governs the `init` conversation, the REANCHOR's text, the package and the reviewer
  guidance. New projects default to English unless a language is explicit; `init` proposes one
  within its normal proposal instead of asking separately. A missing or invalid value falls back to
  the language the REANCHOR is written in, then English. Existing `pt-BR` and legacy Portuguese
  REANCHORs keep producing Portuguese packages.
- **Security and privacy.**
  - `init` declares `disallowed-tools: Write, Edit, NotebookEdit`: it cannot write in the turn
    that produces the proposal; writing becomes possible only after the Owner's next message.
  - Repository content is data: embedded instructions are reported, not followed
    (`collaboration.md` §8).
  - Confidentiality rules (`collaboration.md` §9): secret-holding files are not opened,
    configuration files only when needed, URL user-info is stripped by the command itself.
  - Git runs with `-c core.fsmonitor=false`, without external diff or textconv, and with no output
    options; `ls-remote` runs non-interactively.
  - `init` writes only inside the repository root and never touches agent settings.
- **Packaging.** Local marketplace `auttares` (`.claude-plugin/marketplace.json`, source `./`) for a
  user-scope install; manifests at 0.1.1 with no local-path descriptions; `.gitignore` for local
  settings, environment files and caches.

## 0.1.0 — 2026-10-15 (local, not released)

- Shared protocol (`protocol/collaboration.md`, `protocol/continuity.md`), template
  `templates/REANCHOR.md`, skills `init` and `reanchor`.
- Fix: `reanchor` uses a REANCHOR that exists only in the working tree and declares it unpublished
  (found by test A3).
- Test record in `TESTING.md`.
- License: not yet chosen by the owner.
