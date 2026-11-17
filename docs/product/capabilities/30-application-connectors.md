# Application connectors

Exchange of scenes with the native files and running sessions of other content-creation
applications through connectors, with identifiers that survive repeated round trips. Decision:
[suite ADR-0017](https://github.com/ACKaraca/arc-suite/blob/main/docs/adr/0017-exchange-connectors-and-licensed-bridges.md).

A **connector** is a small add-in that runs inside the user's own licensed copy of the other
application, uses only that application's official public API, and reads or writes the Arc exchange
package. Connectors live in the separate `arc-connectors` repository and are written in the language
the host requires; MeshBuilder itself contains no code of any host application and no connector
loads into its process. This file specifies what MeshBuilder does with the package and the sessions.
Reverse engineering undocumented formats or binaries remains excluded
([vision](../vision.md#non-goals)).

## Domain objects

| Object | Key properties | Relationships |
|---|---|---|
| Exchange package | Schema version, host application and version, units and axes, content sections, identifier map, checksums | Written by a connector or by MeshBuilder; format specified in [`arc-core`](https://github.com/ACKaraca/arc-core/blob/main/docs/architecture/interchange.md) |
| Connector link | Host identifier of each object, document id of the mapped object, last synchronized state hash | One per exchanged document; keyed by host identifiers, stored with the document |
| Live link session | Connector endpoint, direction, content filter, update granularity, state (connected, paused, lost) | Runs against one connector link |
| Licensed bridge | User-installed conversion library, version, separate process, interface | Called for formats where a vendor licenses a library; never distributed with Arc |
| Fidelity report | Per content type: data or edit fidelity, host API features used, unsupported items | Produced by every exchange next to the loss report (MB-IO-002) |

## Package exchange

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-CONNECT-001 | Exchange package import | Reads an Arc exchange package written by a connector and creates or updates scene objects from it, applying units, axes and the identifier map (MB-IO-001, MB-IO-003). Runs as a cancellable background job and produces a loss report. | T3 | 5 |
| MB-CONNECT-002 | Exchange package export | Writes the document, a selection or a collection as an exchange package that a connector applies inside the host application, with the identifier map of the last synchronization. | T3 | 5 |
| MB-CONNECT-003 | Package validation | Checks schema version, checksums and element counts before mapping, rejects malformed or oversized packages with a message and leaves the document unchanged (MB-IO-007). | T3 | 5 |
| MB-CONNECT-004 | Fidelity report | After each exchange lists, per content type, whether data or edit fidelity was reached, which host API features were used and which items the host API does not expose, with the substitution used. | T3 | 5 |
| MB-CONNECT-005 | Connector command parity | Package import, export, merge and live link control are commands available from the CLI and the automation protocol; no exchange step opens a dialog in headless mode. | T3 | 5 |

## Scene content

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-CONNECT-006 | Scene hierarchy exchange | Maps parenting, transforms with pivots and rotation orders, visibility, layers, collections, instances and user properties between host scenes and the document in both directions; unmapped host constructs are kept as preserved foreign data. | T3 | 5 |
| MB-CONNECT-007 | Mesh exchange with modifiers | Maps host meshes with normals, UVs, attributes, creases and material assignments. Host modifiers that have an equivalent become modifier stack nodes with mapped parameters; the others are baked at the evaluated state, and each decision is listed in the fidelity report. | T3 | 5 |
| MB-CONNECT-008 | Material exchange | Maps host materials and texture networks to OpenPBR and MaterialX graphs with texture color spaces, listing approximations (MB-LOOK-026); materials in the other direction are written as the nearest host representation plus baked textures where the host has no equivalent. | T3 | 5 |
| MB-CONNECT-009 | Rig exchange | Maps joint hierarchies, constraints, controls, driven relationships and solver settings that have equivalents in the rigging model; unmapped rig logic is baked to animation on the joints or controls and reported. | T3 | 5 |
| MB-CONNECT-010 | Skinning exchange | Maps bind poses, skin weights per joint, blend shapes with in-between shapes and corrective shapes and their drivers, keeping weights per component identifier. | T3 | 5 |
| MB-CONNECT-011 | Animation exchange | Maps animation curves with tangent types, rotation orders and time units, animation layers with weights and masks, clips and time ranges, and exports curves in both directions without resampling when the target supports the curve type. | T3 | 5 |
| MB-CONNECT-012 | Camera and light exchange | Maps cameras (projection, sensor, focal length, focus, shutter) and lights (kind, color, intensity unit, shape, linking) with animation, converting intensity units and listing host light types without an equivalent. | T3 | 5 |

## Identity and round trips

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-CONNECT-013 | Persistent identifiers | Each exchanged object stores the host identifier and the document id in the connector link, so repeated exports and imports update the same objects, keep names unique among siblings and never duplicate. Identifiers of components are carried where the host exposes stable ones and are otherwise matched by the rules of MB-MOD-006 and reported. | T3 | 5 |
| MB-CONNECT-014 | Round-trip merge | On re-import compares host state, document state and the last synchronized state per object, applies changes made on one side only, and lists objects changed on both sides for a per-object choice (keep document, take host, keep both). Nothing is overwritten before the choice. | T3 | 5 |
| MB-CONNECT-015 | Removed and renamed objects | Detects objects deleted or renamed on either side by identifier and offers to remove, keep or detach them from the link; renames do not break the link. | T3 | 5 |

## Live link sessions

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-CONNECT-016 | Live link session | While both applications run, streams changes between the running host application and the document through the connector, in one or both directions, for the content filter chosen (transforms, geometry, materials, animation, cameras, lights). Granularity is the finest the host's public API reports; where it reports none, the connector sends revision-based differences at a user-set interval. | T3 | 5 |
| MB-CONNECT-017 | Live link control and recovery | A session can be paused, resumed, restricted to a selection and ended; a lost host connection ends the session with a message, keeps the last synchronized state and never changes the document without a transaction. | T3 | 5 |
| MB-CONNECT-018 | Viewport and time synchronization | Optionally synchronizes the active camera, the selection and the current time between the applications during a live session, where the host API exposes them. | T3 | 5 |

## Bridges and archives

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-CONNECT-019 | Licensed bridge | Calls a conversion library that the user has installed and licensed, in a separate bridge process over a neutral interchange stream, for formats where a vendor or consortium licenses such a library. The library is never distributed with Arc and no core workflow requires it. | T3 | 5 |
| MB-CONNECT-020 | Bridge isolation | A bridge process runs with bounded input size, memory and time, without network access, and ends with a message on failure; its crash never affects the document or the application (MB-RENDER-012 sets the same rule for renderers). | T3 | 5 |
| MB-CONNECT-021 | Procedural material archives | Archives whose graph format has no published specification or open runtime are exchanged through the connector in the host application, which evaluates them there and delivers MaterialX graphs for nodes that map and baked textures for the rest (MB-LOOK-015, MB-LOOK-027). | T3 | 5 |
| MB-CONNECT-022 | Connector discovery | Lists connectors and bridges available on the machine with host, version, supported content types and the API features each relies on, and explains how to install a missing one when an exchange needs it. | T3 | 5 |
