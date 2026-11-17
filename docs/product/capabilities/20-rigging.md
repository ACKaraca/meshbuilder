# Rigging

This area specifies how MeshBuilder builds the control structure that makes a model animatable: joints, solvers, constraints, control objects, driven relationships and generated or modular rigs. A rig is a typed graph of nodes evaluated by the shared node engine, so the same rig can be authored by hand, generated from guides, or assembled from reusable components, and it stays validated, profiled and undoable like any other document content.

## Domain objects

| Object | Key properties | Relationships |
|---|---|---|
| Joint | Stable ID, name, parent, local transform, joint orient, rotate order, radius, label (side, type), rotation limits, segment scale compensation, rest transform | Child of a joint or transform; influences a bound mesh (see SKIN); target of constraints, solvers and drivers |
| Skeleton | Root joint, ordered joint list, rest pose, label map | Owns Joints; referenced by Skin Bindings, Character Definitions (MOCAP) and Pose Libraries |
| Solver Chain | Start joint, end joint, solver type, goal transform, pole target, twist and stretch settings, blend weight | Reads Joints and Controls; writes Joint rotations; created per limb or spine |
| Constraint | Kind, constrained object, target list with weights, offset, axis filter, interpolation mode, enabled flag | Reads target transforms; writes the constrained transform; weights can be animated or driven |
| Control | Control shape, color, size, zero transform, locked and hidden channels, custom attributes, rotate order | A transform that animators edit; drives Joints, Solver Chains and Constraints |
| Driven Relationship | Driver attribute, driven attribute, response curve with in-betweens, range, pre/post extrapolation | Connects any two animatable attributes; stored as a node in the rig graph |
| Rig Component | Component type, guide inputs, outputs, parameters, side, build version | Generates Joints, Controls and nodes; lives in a Rig Assembly |
| Rig Assembly | Ordered Rig Components, published attributes, build version, validation state | Owns a Skeleton and Controls; referenced from a scene as a rig asset |
| Rig Template | Component list, guide layout, naming rules | Instantiates a Rig Assembly from guides |
| Validation Report | Rule results, severity, offending object IDs, available fixes | Produced for a Rig Assembly; consumed by the UI, command line and Hub jobs |
| Pose | Named set of control and attribute values, scope (full body, body part, selection), mirror map | Stored in a Pose Library; applied to a Rig Assembly through stable control IDs |
| Pose Library | Pose list, thumbnails, categories, target rig type | Referenced by animation tools and character files |

## Joints and skeletons

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-RIG-001 | Joint creation | Places joints by clicking in the viewport, on a surface, or on a curve; each new joint becomes a child of the previously placed joint and the command records the whole chain as one transaction. | T1 | 4 |
| MB-RIG-002 | Joint orientation | Orients a chain so the primary axis aims at the child and the secondary axis follows a chosen up vector or plane normal; end joints copy the parent orientation. The result is stored as joint orient so rotation channels start at zero. | T1 | 4 |
| MB-RIG-003 | Orientation check | Displays local axes for any selection and reports joints whose axes flip against the parent by more than a tolerance. The report offers a fix that re-orients the flagged joints without changing world pose. | T1 | 4 |
| MB-RIG-004 | Mirror joints | Mirrors a joint chain across a chosen plane in either behavior mode (mirrored rotations produce mirrored motion) or orientation mode (axes copy), with search-and-replace renaming. Limits, labels and radius copy to the mirrored side. | T1 | 4 |
| MB-RIG-005 | Joint labeling | Assigns each joint a side (left, right, center, none) and a type from a standard list or a custom name. Mirroring, weight mirroring, retargeting and pose mirroring read these labels instead of parsing names. | T1 | 4 |
| MB-RIG-006 | Insert and remove joints | Inserting splits a segment and keeps the pose and any bound weights continuous; removing a joint reparents its children while preserving world transforms and redistributes its skin weights to the parent. | T1 | 4 |
| MB-RIG-007 | Edit joint position | A joint-adjust mode moves a joint while holding its children and any bound mesh in place by compensating local transforms. | T1 | 4 |
| MB-RIG-008 | Joint limits | Stores per-axis rotation and optional translation limits with lock flags; solvers and manipulators clamp to them, and a limit cone is drawn in the viewport. | T1 | 4 |
| MB-RIG-009 | Rest pose and reset | Stores a rest pose per skeleton; a reset command returns all joints or a selection to rest, and the rest pose is the reference for binding and retargeting. | T1 | 4 |
| MB-RIG-010 | Segment scale compensation | A per-joint switch stops a joint from inheriting its parent's scale, so stretchy limbs do not shear their children. | T2 | 4 |
| MB-RIG-011 | Joint display | Draws joints as bones, spheres or lines with adjustable radius, X-ray, color by label and per-layer visibility. | T1 | 4 |
| MB-RIG-012 | Joint chain from curve | Builds a joint chain at equal spacing or equal arc length along a curve, with a count parameter, for spines, tails and spline solvers. | T2 | 4 |
| MB-RIG-013 | Skeleton snapshot | Saves a skeleton's hierarchy, orient, labels, limits and rest pose as a named asset that can be re-applied to another scene or compared against a character definition. | T2 | 4 |

## Solvers and switching

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-RIG-014 | Two-bone limb solver | Solves a three-joint chain analytically to reach a goal, with a pole target controlling the bend plane and a twist angle; the result is exact and repeatable for the same inputs. | T1 | 4 |
| MB-RIG-015 | Multi-joint rotate-plane solver | Solves chains of any length toward a goal with a pole direction and twist, iterating to a tolerance and iteration limit stored on the solver. | T1 | 4 |
| MB-RIG-016 | Single-chain solver | Solves a chain so the end joint aims at the goal position without a pole direction, for tails, antennae and simple rigs. | T2 | 4 |
| MB-RIG-017 | Spline solver | Drives a joint chain along a curve; the curve's control points are driven by controls or clusters. Start and end twist, a roll ramp, stretch to curve length and optional volume preservation are parameters. | T1 | 4 |
| MB-RIG-018 | Spring-biased solver | A chain solver for four-legged limb geometry that keeps a preferred bend direction at the middle joint during extreme goal positions. | T3 | 4 |
| MB-RIG-019 | IK and FK blending | A blend attribute from 0 to 1 mixes the solver result with the forward-kinematic rotations of the same joints; both result sets stay available for display. | T1 | 4 |
| MB-RIG-020 | IK/FK pose-matching switch | Switching a limb between IK and FK computes the other mode's controls so the visible pose does not change, and records the match on the current frame when auto-key is on. | T1 | 4 |
| MB-RIG-021 | Pole vector control | Creates a pole target placed on the bend plane at a chosen distance and optionally draws a guide line to the middle joint; the target can be parented or space-switched. | T1 | 4 |
| MB-RIG-022 | Limb stretch and soft reach | An optional stretch scales limb segments when the goal exceeds chain length up to a limit; a soft-reach ease removes the snap at full extension. | T2 | 4 |
| MB-RIG-023 | Twist distribution | Distributes forearm, upper arm, thigh and shin twist across helper joints by weight so skinned surfaces twist smoothly; weights are editable per helper. | T1 | 4 |
| MB-RIG-024 | Foot roll setup | Creates heel, ball, toe and side pivots driven by roll and bank attributes on the foot control; the setup is part of the leg component and mirrors with it. | T2 | 4 |
| MB-RIG-025 | Follow-through chain | A chain whose rotations lag and overshoot the parent motion with stiffness, damping and gravity parameters, evaluated deterministically from time and cached with the animation. | T2 | 5 |
| MB-RIG-026 | Solver priority and stickiness | Solver chains sharing joints evaluate in a stored priority order; a sticky option holds the goal in world space while parents move. | T2 | 4 |

## Constraints

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-RIG-027 | Point constraint | Moves the constrained object toward the weighted average of one or more target positions, with an optional offset and per-axis filter. | T1 | 4 |
| MB-RIG-028 | Orient constraint | Rotates the constrained object to the weighted blend of target orientations using shortest-path quaternion interpolation, with an offset and per-axis filter. | T1 | 4 |
| MB-RIG-029 | Scale constraint | Applies the weighted blend of target scales to the constrained object with an optional offset and per-axis filter. | T2 | 4 |
| MB-RIG-030 | Aim constraint | Aims one axis of the constrained object at one or more targets while a second axis follows an up vector (object, world, or none); the weight and aim axis are animatable. | T1 | 4 |
| MB-RIG-031 | Parent constraint | Makes the constrained object follow the weighted blend of target transforms, translation and rotation together, with offsets maintained at creation time when requested. | T1 | 4 |
| MB-RIG-032 | Path constraint | Places an object along a curve by a parameter in length or percent, with optional follow orientation, up vector, banking and loop. | T1 | 4 |
| MB-RIG-033 | Surface attachment | Attaches an object to a point on a mesh or NURBS surface by face and barycentric coordinates or UV, and keeps it attached as the surface deforms or animates. | T1 | 4 |
| MB-RIG-034 | Closest-point and normal constraints | A geometry constraint keeps an object on the closest point of a target surface; normal and tangent constraints align an axis to the surface normal or tangent at that point. | T2 | 4 |
| MB-RIG-035 | Maintain offset | Any constraint can capture the current relative transform at creation so the object does not jump; the offset is stored and editable. | T1 | 4 |
| MB-RIG-036 | Animatable weights and multiple targets | Each target has a weight that is keyable and drivable; weights are normalized only when the constraint type requires it, and the evaluation order is the target list order. | T1 | 4 |
| MB-RIG-037 | Space switching | A space switch constraint parents a control to a list of named spaces (world, root, hand, head) and changes space on a chosen frame without moving the control, writing compensating keys. | T1 | 4 |
| MB-RIG-038 | Matrix-based constraints | Lightweight aim, blend, pick and parent operations are exposed as graph nodes operating on matrices, evaluated without history and safe for parallel evaluation. | T2 | 4 |
| MB-RIG-039 | Constraint cycle detection | Creating a constraint that would produce a dependency cycle is refused with the path of nodes that forms the cycle. | T1 | 4 |
| MB-RIG-040 | Constraint bake and removal | Baking replaces a constraint by keys on the constrained object over a frame range; removal can keep the current world transform or restore the pre-constraint local values. | T1 | 4 |

## Controls and driven relationships

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-RIG-041 | Control creation and shape library | Creates a control from a library of curve shapes (circle, box, arrow, pointer, gimbal, cross and others), with color, size and editable shape points; the shape is independent of the control's transform. | T1 | 4 |
| MB-RIG-042 | Zero transform | Every control sits under an offset transform that holds its rest placement, so the control's own channels read zero at rest and reset returns it to the rest pose. | T1 | 4 |
| MB-RIG-043 | Channel locking and limits | Per-channel lock, hide, keyable state and numeric limits on translation, rotation, scale and custom attributes; hidden or locked channels are excluded from key and pose commands. | T1 | 4 |
| MB-RIG-044 | Custom attributes | Adds typed attributes (float, integer, boolean, enumeration, string, vector) with range, default and keyable flag to any object, including enumerations used as switches. | T1 | 4 |
| MB-RIG-045 | Driven relationships | Connects a driver attribute to a driven attribute through an editable response curve with in-betweens and extrapolation; multiple drivers on one attribute combine by a stated mode. | T1 | 4 |
| MB-RIG-046 | Rig expressions | Evaluates a bounded, unit-aware expression over attributes and time to produce a value; expressions run through the same engine as sketch and parameter expressions and are marked as serial or parallel-safe. | T2 | 4 |
| MB-RIG-047 | Rig math and utility nodes | Provides graph nodes for arithmetic, condition, clamp, remap, blend, angle between, distance between, closest point and matrix compose and decompose, so relationships can be built without scripts. | T1 | 4 |
| MB-RIG-048 | Attribute linking | Links one attribute to another directly or through a utility node, with a dependency list that shows every driver and driven attribute of a selection. | T1 | 4 |
| MB-RIG-049 | Control mirroring and symmetry | Controls, drivers and constraints created on one side mirror to the other side by label, with a choice of behavior or orientation mirroring. | T1 | 4 |
| MB-RIG-050 | Selection sets and picker | Named sets of controls can be selected from a dockable picker panel that the rig author lays out graphically; the panel is stored with the rig asset. | T2 | 4 |
| MB-RIG-051 | Face control setup | Builds a facial control layout (brows, eyelids, mouth corners, jaw, tongue, eye aim) that drives blend shape weights and joints through driven relationships. | T2 | 4 |

## Generated and modular rigs

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-RIG-052 | Biped rig generation | Places guides on a humanoid mesh and builds a full-body skeleton, IK/FK limbs, spine, neck, head, hands with finger controls and foot roll, plus a control set and picker. Spine, neck, finger and toe counts are parameters. | T1 | 4 |
| MB-RIG-053 | Quadruped rig generation | Builds a four-legged skeleton and controls from guides with spring-biased legs, a spine and tail chain, neck and head, optional ears and wings. | T2 | 4 |
| MB-RIG-054 | Guide placement from mesh | Proposes guide positions from the mesh volume and landmarks; the author adjusts guides and rebuilds, and the build reports guides that fall outside the mesh. | T2 | 4 |
| MB-RIG-055 | Rig component library | Each rig part (arm, leg, spine, neck, finger, tail, eye, wheel, generic chain) is a component defined as a node graph with guide inputs, parameters and outputs; users author and share components in the same editor. | T1 | 4 |
| MB-RIG-056 | Rig assembly and rebuild | Components connect in an assembly by parent and output links. Rebuilding regenerates joints, controls and nodes from guides and parameters, and animation survives because controls keep stable IDs. | T1 | 4 |
| MB-RIG-057 | Published rig interface | An assembly publishes a chosen set of attributes, controls and sets; other parts of the scene and asset references may edit only published items, and everything else is locked. | T1 | 4 |
| MB-RIG-058 | Rig replacement | A new build of a rig can replace an older one in a scene; animation and constraints transfer through stable control IDs and a mapping report lists controls with no counterpart. | T2 | 4 |
| MB-RIG-059 | Rig validation | Checks naming and label completeness, flipped orientation, non-zero rest channels, constraint cycles, unbound or unweighted joints, unpublished controls, missing limits and serial-only nodes, with a one-click fix where one exists. | T1 | 4 |
| MB-RIG-060 | Headless rig validation | The same checks run from the command line and automation protocol and return a machine-readable report, so a Hub job can reject a rig before it is published. | T2 | 4 |
| MB-RIG-061 | Pose library | Stores poses by control set with thumbnails and categories, applies them with a blend percentage, to a selection or body part, and mirrored. Poses address controls by stable ID and survive rig rebuilds. | T1 | 4 |
| MB-RIG-062 | Pose and animation transfer between rigs of one template | Copies a pose or animation range between two assemblies built from the same template, by control ID, with a report for unmatched controls. | T2 | 4 |
| MB-RIG-063 | Rig evaluation profile | A profiler shows per-node evaluation time and marks each node parallel-safe, serial, or accelerated; the author can open the slowest nodes from the report. | T1 | 4 |
| MB-RIG-064 | Rig proxy | A rig may define a low-cost proxy deformation or proxy geometry used during playback and viewport work and switched to the full deformation for caching and final output. | T2 | 4 |
| MB-RIG-065 | Rig export | Exports a skeleton, skin and animation bake to glTF, FBX and OpenUSD skeletal data, baking constraints and drivers that the target format cannot express and listing what was baked. | T1 | 4 |
| MB-RIG-066 | Assisted guide proposal | An optional assistant proposes joint positions and a template choice from a mesh preview; the proposal is shown as guides that the user accepts, edits or discards before any rig is built. | T3 | 5 |
