# 0004. FBX through an open reader, with a vendor library only in a separate process

- Status: Accepted (revised)
- Capabilities: MB-IO-010, MB-CONNECT-019, MB-CONNECT-020

## Context

FBX remains a common exchange format for animation and game pipelines, but it has no published
specification, and its reference software development kit is distributed under a proprietary license
that is not on the suite's dependency allowlist. The suite policy
([ADR-0017](https://github.com/ACKaraca/arc-suite/blob/main/docs/adr/0017-exchange-connectors-and-licensed-bridges.md))
reads a format natively when a permissively licensed open reader exists, and allows a vendor library
only as a user-installed, user-licensed copy in a separate process.

## Decision

FBX import and export use an open reader and writer that meets the dependency allowlist and passes
the FBX round-trip corpus. It runs in process behind `arc-io-mesh`, is fuzzed and bounded like every
importer, and produces a loss report. FBX features it does not cover (for example some take or
constraint data) are kept as preserved foreign data. A vendor conversion library is never linked or
distributed; where a user has installed and licensed one, a bridge executable calls it in a separate
process over a neutral interchange stream (a USD layer plus a sidecar for FBX-specific data), and
the application selects it only by user choice. No workflow requires the bridge.

## Consequences

- The common FBX case needs no second process and no separate install.
- The core application stays within the license allowlist, and a vendor library cannot crash it.
- The open reader's coverage defines what the in-process path offers; gaps are listed in loss reports
  and can be closed by the bridge.
- Workflow W09 verifies that USD export remains available without any FBX component.

## Alternatives considered

- **Link the proprietary SDK directly.** Violates the license policy and puts a closed library in the
  crash domain.
- **Reverse engineer the binary format.** Prohibited by the clean-room policy.
- **Bridge process only.** Costs a process hop and a separate install for the common case, with no
  benefit once an open reader passes the corpus.
- **No FBX support.** Blocks adoption in animation and game pipelines.

## Reopen when

The open reader fails the FBX round-trip corpus on a significant share of target pipelines
([OQ-18](../product/open-questions.md)), or demand for FBX disappears.
