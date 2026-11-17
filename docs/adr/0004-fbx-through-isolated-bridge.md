# 0004. FBX through an isolated bridge process

- Status: Accepted
- Capabilities: MB-IO-010

## Context

FBX remains a common exchange format for animation and game pipelines, but it has no published
specification, and its reference software development kit is distributed under a proprietary license
that is not on the suite's dependency allowlist. The suite interoperability policy allows a library
under a restrictive license only behind a byte-level bridge in its own process, never required for a
core workflow.

## Decision

FBX import and export run in a separate bridge executable that converts between FBX files and a
neutral interchange stream defined by `meshbuilder-io` (a USD layer plus a sidecar for FBX-specific
data such as takes). The main application never links an FBX library. The bridge is an optional
component and is not needed for any workflow that USD, glTF or Alembic can serve. If an allowlisted
FBX implementation that passes the corpus tests becomes available, it may replace the bridge
internals without changing the interface.

## Consequences

- The core application stays within the license allowlist, and malformed FBX files cannot crash it.
- FBX operations cost one process hop and one conversion, acceptable for file exchange.
- Users who need FBX install the bridge separately; the application explains how when FBX is
  requested and the bridge is absent.
- Workflow W09 verifies that USD export remains available without the bridge.

## Alternatives considered

- **Link the proprietary SDK directly.** Violates the license policy and puts a closed library in the
  crash domain.
- **Reverse engineer the binary format.** Prohibited by the clean-room policy.
- **No FBX support.** Blocks adoption in animation and game pipelines.

## Reopen when

An allowlisted FBX implementation passes the FBX round-trip corpus, or demand for FBX disappears in
target pipelines ([OQ-18](../product/open-questions.md)).
