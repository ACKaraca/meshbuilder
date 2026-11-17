# AGENTS.md

## What this repository is

MeshBuilder: the Arc suite's application for polygon and subdivision modeling, sculpting, texturing,
rigging, animation, motion capture and effects. Native Rust on `arc-core`; native file `.meshb`.
Renders through RenderBuilder over the Arc Scene contract.

## Status and where to start

Design phase: no code and no builds exist. Read in this order: [docs/README.md](docs/README.md),
[vision](docs/product/vision.md), [capability index](docs/product/capabilities/README.md),
[architecture overview](docs/architecture/overview.md), [evaluation](docs/architecture/evaluation.md),
[domain model](docs/architecture/domain-model.md), [roadmap](docs/roadmap.md).

## Repository layout (target)

```
crates/meshbuilder-model/     domain objects, component identity, geometry operations, evaluation nodes (headless)
crates/meshbuilder-commands/  user-level commands and prompt machines (headless)
crates/meshbuilder-io/        format mapping, USD stage hosting, Arc Scene export (headless)
crates/meshbuilder-ui/        tools, editors, manipulators (egui)
apps/meshbuilder/             desktop application (wiring only)
apps/meshbuilder-cli/         headless CLI and automation protocol server
docs/                         product, architecture, roadmap, ADRs
xtask/                        workspace tasks
```

## Commands

No build exists yet. Once code lands, these apply:

- `cargo fmt --check`
- `cargo clippy --workspace --all-targets -- -D warnings`
- `cargo nextest run --workspace`
- `cargo xtask layers`

## Suite rules

These rules apply in every Arc repository. The canonical text is
[`docs/engineering/agent-rules.md`](https://github.com/ACKaraca/arc-suite/blob/main/docs/engineering/agent-rules.md)
in `arc-suite`; change it there first.

1. **A person is accountable.** Whoever runs an agent answers for what it submits.
2. **Start from an issue.** Search issues and pull requests, check that nobody else is assigned,
   assign yourself and set the status to *In Progress* before changing files. When you finish,
   comment with what changed, the commit or pull request link and open items, then update the status.
   *Done* requires met and verified acceptance criteria.
3. **Verify, do not guess.** Open a definition before using it. Confirm a cause in the code.
4. **The document is the authority and everything is a command.** No change reaches a document
   except through a validated command inside a transaction. Programmatic paths never open dialogs.
5. **Respect the layers.** A crate depends only on lower layers. Nothing below the UI layer depends
   on windowing, GPU or UI crates. Products never depend on other products.
6. **Never crash, never lose work.** No `unwrap`, `expect`, `panic!`, `todo!` or `unreachable!` in
   production code. No `unsafe` outside the FFI crates named in an ADR. Treat every file and message
   as hostile: bound recursion, allocation and counts. A crash fix ships with a regression test.
7. **Clean room.** Implement from published specifications, observed behavior and original
   reasoning. Never copy or consult another product's code, decompiled output, file-format reverse
   engineering, documentation text, icons, fonts, templates or sample files. Do not name our
   features after another company's features.
8. **Licenses.** Add a dependency only if its license is on the allowlist in
   `docs/engineering/dependency-policy.md` in `arc-suite`. Record every asset's origin and license.
9. **Rust only in desktop applications.** No web view, no JavaScript or TypeScript.
10. **Small, tested changes.** One concern per pull request, with the test that fails without it.
    Update the documents that describe the behavior you change in the same pull request.
11. **Report only what ran.** Say what you tested and what you could not run.
12. **Ask a human before** adding a license outside the allowlist, publishing or releasing,
    changing a file format or public protocol, adding a new repository or remote, spending money,
    handling secrets, or any destructive git operation. Otherwise keep working.
13. **Keep secrets and personal data out** of repositories, logs and requests.
14. **Commit directly to `main` for now.** Until the project has a second maintainer and this
    repository has reached its MVP release (for `arc-suite` and `arc-core`: the first product MVP),
    work on `main`: no feature branches and no pull requests. Wherever these rules or the documents
    say "pull request", read "commit". Run the checks before every push and name the issue in the
    commit message. This rule is temporary and is deleted from a repository when that point is
    reached.

## Repository invariants

- One evaluation graph: the modifier stack is a view of the object's geometry graph; implement each
  geometry operation once in `meshbuilder-model` and expose it as command, modifier and node (ADR-0001).
- Components are addressed by stable identifiers, never indices, in selections, recorded edits,
  overrides and transfers; every operation defines identity propagation (ADR-0002).
- Evaluation nodes are pure, declare their reads and time dependency, and are deterministic across
  threads and platforms; random operations take explicit seeds.
- Results carry the revision they were computed from; stale results are never applied.
- Storage is metres, radians, Z-up; time is integer ticks at 705,600,000 per second.
- `.meshb` is the authority; USD stages are hosted and edited through commands; native content is
  exported to USD as evaluated results (ADR-0003).
- Never link a path tracer; render through the Arc Scene contract. Other renderers run as Hydra
  render delegates in a separate process (ADR-0007).
- FBX uses an open reader behind `arc-io-mesh`; never link a vendor FBX library, use the separate
  bridge process (ADR-0004). Connectors exchange the Arc exchange package and never load in process.
- Documents never contain code that runs on open.
- Dense data is stored as chunked package assets (ADR-0006).
- Every importer bounds input and produces a loss report.

## Documentation rules

- No mention of planning material or conversations, and no phrasing that presents MeshBuilder as
  derived from, a copy of, or a substitute for another product; never describe a feature by another
  product's feature.
- Other companies' names only as formats and standards, factual interoperability statements, the one
  positioning paragraph in `docs/product/vision.md`, and library names in architecture text.
- Use our own descriptive vocabulary; generic industry terms are fine.
- Each fact once; link suite-wide rules in `arc-suite` instead of copying them.
- Trademark notice stays the last section of `README.md`.
- Clean room: original wording; no copied structure, dialog text, option names or default lists.
- Capability identifiers `MB-<AREA>-<NNN>` are permanent; never reuse a number.

## Issue workflow

Start from an issue; search issues and pull requests first; confirm nobody is assigned; assign
yourself; set *In Progress*. Statuses: Backlog, Ready, In Progress, In Review, Blocked, Done (only
when acceptance criteria are met and verified). On finishing, comment with what changed, the link and
open items; update status; file follow-up issues after searching for duplicates. Labels: `type:`,
`tier:`, `phase:`, `area: <token>`, `status: blocked`, `needs: verification`. Conventional Commits
with DCO sign-off.

## Verification

Run the narrowest relevant check first (the affected crate's tests, then clippy and fmt), broaden
only when it fails for a relevant reason or shared code changed. Report exactly which commands ran
and which could not.

## Ask a human about

File-format or automation-protocol changes, new dependencies outside the allowlist, changes to
component identity or evaluation determinism rules, new crates, ownership of capabilities shared with
another area, and anything listed in suite rule 12.
