# Assistance

This area specifies what the optional assistant does in MeshBuilder: answering questions about a
scene, explaining and repairing mesh problems, guiding retopology, unwrapping and skin weights,
cleaning up motion capture and animation, tagging and finding assets, assigning materials, drafting
graphs and scripts, and producing rough starting meshes with clear provenance, always as previewed
commands checked by the product's mesh, rig, stage and export checks. The suite rules for levels,
grounding, data handling, provenance and evaluation are in
[assistance](https://github.com/ACKaraca/arc-suite/blob/main/docs/architecture/assistance.md); the
panel is [MB-SCRIPT-018](17-scripting-and-extensibility.md) and the protocol endpoint is
[MB-SCRIPT-011](17-scripting-and-extensibility.md). Assisted operations specified with their own
areas stay there: rig guides ([MB-RIG-066](20-rigging.md)), material layers from a reference
([MB-PAINT-036](26-texture-painting-and-baking.md)), sculpt and retopology density
([MB-SCULPT-055](25-sculpting.md)) and in-betweening, locomotion and pose polish
([MB-ANIM-048 to MB-ANIM-051](22-keyframe-animation.md)). Every capability here also has a manual
and scripted path.

## Domain objects

| Object | Key properties | Relationships |
|---|---|---|
| Assistance request | Text, level, scope (objects, collections, stage prims, frame range), protected objects, base revision | Produces answers or proposals |
| Proposal | Items, command invocations, check results, before and after metrics | Bound to a base revision; applied as one undo step |
| Generated starting mesh | Source (text or image), provider and model, date, license terms recorded by the user, provenance mark | A separate object; never merged silently into an authored mesh |
| Asset tag set | Tags, source of each tag (name, geometry statistics, rendered thumbnail), confirmation state | Stored on library assets after confirmation |

## Questions and checks

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ASSIST-001 | Scene questions | The assistant answers questions such as "which objects exceed 20 000 triangles", "which materials have missing textures" or "which prims override this asset" from scene and stage queries, citing objects and prims that select on request. | T2 | 4 |
| MB-ASSIST-002 | Mesh problems explained | The assistant runs the mesh check and watertight check (MB-TOPO-023, MB-TOPO-025) and subdivision topology checks (MB-SUBD-016), explains each finding with its location, and proposes repairs such as weld and clean (MB-MOD-032) that the same checks evaluate. | T3 | 4 |
| MB-ASSIST-003 | Stage diagnostics explained | For asset path diagnostics and stage validation (MB-USD-021, MB-USD-022), the assistant explains unresolved paths, conflicting opinions and composition problems, and proposes path fixes that a new validation evaluates. | T3 | 4 |
| MB-ASSIST-004 | Export readiness | Against a target profile (triangle, material and bone budgets, UV sets, scale, naming), the assistant runs export validation (MB-IO-006), explains failures and proposes fixes, including level-of-detail meshes by decimation (MB-TOPO-014) whose triangle counts and silhouette deviation are measured. | T3 | 4 |

## Topology, UVs and deformation

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ASSIST-005 | Retopology guidance | For a dense mesh and a stated use (animated face, hard-surface prop, real-time asset), the assistant proposes target counts, edge-loop guide curves around deforming regions and settings for automatic quad retopology (MB-TOPO-013); topology checks and deviation from the source are shown for the result. | T3 | 5 |
| MB-ASSIST-006 | Unwrapping proposals | The assistant proposes seams that follow hard edges and hidden areas, packing groups and a texel density target, using automatic seams, unwrap and pack (MB-UV-009, MB-UV-016, MB-UV-017); distortion and texel density measures are shown per island before apply. | T3 | 4 |
| MB-ASSIST-007 | Skin weight repair | Starting from automatic initial weights (MB-SKIN-005), the assistant poses the rig through test poses from the pose library (MB-RIG-061), finds regions with volume loss, collapse or interpenetration, and proposes smoothing, mirroring or transfer (MB-SKIN-014, MB-SKIN-016, MB-SKIN-018) whose effect is measured on the same poses. | T3 | 4 |
| MB-ASSIST-008 | Rig validation explained | After rig validation (MB-RIG-059), the assistant explains each finding (orientation, missing controls, cycles) and proposes fixes that a new validation run evaluates. | T3 | 4 |

## Motion

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ASSIST-009 | Motion capture cleanup | For imported marker data, the assistant runs gap detection, swap repair, spike filtering and labeling (MB-MOCAP-007 to MB-MOCAP-011) in a proposed order with parameters, and explains solve diagnostics (MB-MOCAP-015); the diagnostics run again on the result. | T3 | 4 |
| MB-ASSIST-010 | Animation cleanup | On a keyed or captured motion, the assistant finds foot sliding, pops and jitter by contact and velocity measures, and proposes key reduction (MB-MOCAP-041), contact locks and filter settings on an additive layer, with before and after curves. | T3 | 5 |

## Content and assets

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ASSIST-011 | Asset tagging | For assets in a library (MB-ASSET-016, MB-ASSET-017), the assistant proposes tags, categories and descriptions from names, geometry statistics and a rendered thumbnail; tags apply only after the user confirms them, each with its source. | T3 | 4 |
| MB-ASSIST-012 | Asset search | The user searches local libraries in natural language ("worn wooden crate, under 2 000 triangles"); the assistant combines confirmed tags with geometry and material filters and lists matching assets with the reasons for each match. | T3 | 4 |
| MB-ASSIST-013 | Material assignment | From a request such as "brushed steel on all handles, rubber on the feet", the assistant proposes assignments of materials from the material libraries (MB-LOOK-024) to the matching objects or faces, previewed in the viewport. | T3 | 4 |
| MB-ASSIST-014 | Graph and script drafting | The assistant drafts a procedural node graph or a Python script for a described task, shown as a graph or source with an explanation; it runs only when the user starts it, and documents never carry code that runs on open (MB-SCRIPT-010). | T3 | 4 |
| MB-ASSIST-015 | Rough mesh from text or image | Through a user-configured generation endpoint, the assistant creates a rough mesh from a text description or reference image as a separate object marked as generated, with the source, provider, model and the license terms the user recorded; mesh checks run on it, and it is intended as a starting point for modeling. | T3 | 5 |

## Review

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ASSIST-016 | Assisted content review | Objects, weights, keys and assets carrying a provenance mark, including generated meshes, show a distinct overlay and filter; the user reviews and clears marks, and exporting or publishing lists unreviewed and generated content first. | T3 | 4 |

## Readiness

Readiness classes are defined in
[assistance](https://github.com/ACKaraca/arc-suite/blob/main/docs/architecture/assistance.md#readiness-classes).

| Class | ID | Checked by | Depends on |
|---|---|---|---|
| R1 | MB-ASSIST-001 | Scene and stage queries | MB-SCRIPT-011, MB-SCRIPT-018 |
| R1 | MB-ASSIST-002 | Mesh, watertight and topology checks | MB-TOPO-023, MB-TOPO-025, MB-SUBD-016 |
| R1 | MB-ASSIST-003 | Stage validation | MB-USD-021, MB-USD-022 |
| R1 | MB-ASSIST-004 | Export validation, decimation measures | MB-IO-006, MB-TOPO-014 |
| R2 | MB-ASSIST-005 | Topology checks, deviation from source | MB-TOPO-013 |
| R1 | MB-ASSIST-006 | Distortion and texel density measures | MB-UV-009, MB-UV-016, MB-UV-017 |
| R2 | MB-ASSIST-007 | Deformation measures on test poses | MB-SKIN-005, MB-RIG-061, a deformation quality measure |
| R1 | MB-ASSIST-008 | Rig validation | MB-RIG-059 |
| R1 | MB-ASSIST-009 | Solve diagnostics | MB-MOCAP-007 to MB-MOCAP-011, MB-MOCAP-015 |
| R2 | MB-ASSIST-010 | Contact and velocity measures | MB-MOCAP-041, a foot contact detector |
| R1 | MB-ASSIST-011 | User confirmation | MB-ASSET-016, MB-ASSET-017, image input in the provider |
| R1 | MB-ASSIST-012 | Library filters | MB-ASSET-015, MB-ASSIST-011 |
| R1 | MB-ASSIST-013 | Viewport preview | MB-LOOK-024 |
| R1 | MB-ASSIST-014 | Static check of scripts and graphs | MB-SCRIPT-010, node graphs |
| R3 | MB-ASSIST-015 | Mesh check | A user-configured generation endpoint |
| R1 | MB-ASSIST-016 | Provenance query | Provenance marks in `arc-core` |

## Evaluation

The assistance task suite is built from the [reference workflows](../workflows.md): W01
(MB-ASSIST-002, MB-ASSIST-004, MB-ASSIST-006), W02 (MB-ASSIST-005, MB-ASSIST-006), W03
(MB-ASSIST-014), W06 (MB-ASSIST-003), W07 (MB-ASSIST-013), W08 (MB-ASSIST-007, MB-ASSIST-008),
W09 (MB-ASSIST-009, MB-ASSIST-010), W12 (MB-ASSIST-002, MB-ASSIST-004) and W13 (MB-ASSIST-004).
Thresholds are settled under [OQ-22](../open-questions.md).
