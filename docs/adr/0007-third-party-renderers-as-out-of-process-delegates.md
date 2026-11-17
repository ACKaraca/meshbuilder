# 0007. Third-party renderers as out-of-process Hydra render delegates

- Status: Accepted
- Capabilities: MB-RENDER-022 to MB-RENDER-030

## Context

Studios use several renderers on one scene. MeshBuilder renders through RenderBuilder over the Arc
Scene contract and never links a path tracer. Renderers from other vendors are distributed as native
libraries with their own licenses, threading and failure modes, and the suite does not allow native
plug-ins in the application. Suite
[ADR-0017](https://github.com/ACKaraca/arc-suite/blob/main/docs/adr/0017-exchange-connectors-and-licensed-bridges.md)
applies the same separate-process principle to licensed bridges.

## Decision

A render delegate host executable loads one user-installed Hydra render delegate per session in its
own process. MeshBuilder feeds it the evaluated scene as a USD stage with incremental updates and
receives images and pass products over local IPC and shared memory. The renderer is selected per
viewport pane and for final frames. The application process never loads a delegate, and the host has
no write access to the document. A watchdog ends sessions that hang or exceed memory limits.

## Consequences

- A delegate crash ends one session with a message and never affects the document.
- Scene translation to USD is the single hand-off, so delegates see the same scene as USD export
  (MB-RENDER-021), with no renderer-specific code in the model.
- Material fidelity depends on what each delegate supports; approximations are reported.
- Interactive latency includes one process hop and one scene translation.

## Alternatives considered

- **Load delegates in process.** Fastest path, but puts closed native code in the crash and license
  domain and violates the rule against native plug-ins.
- **Export USD and render offline only.** Remains available (MB-RENDER-021) but offers no interactive
  or per-viewport use.
- **Per-renderer exporters.** Multiplies translation code with no shared contract.

## Reopen when

Delegate sessions cannot meet interactive latency targets through shared memory, or the delegate
interface changes incompatibly.
