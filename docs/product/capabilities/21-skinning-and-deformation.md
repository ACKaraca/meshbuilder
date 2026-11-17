# Skinning and deformation

This area specifies how MeshBuilder deforms geometry from a rig: binding meshes to joints, authoring and transferring weights, blend shapes and pose-driven corrections, shape deformers, smoothing deformers, muscle and collision response, and learned deformers trained from a high-quality rig. Deformers are nodes in an ordered, inspectable chain on a mesh and evaluate in parallel wherever the node contract allows.

## Domain objects

| Object | Key properties | Relationships |
|---|---|---|
| Skin Binding | Bind method, influence list, max influences per vertex, bind pose, normalization mode, falloff settings | Binds one mesh to a Skeleton's Joints and optional influence objects; owns Weight Layers |
| Influence | Joint or influence object reference, lock flag, bind transform | Member of a Skin Binding; contributes weight per vertex |
| Weight Layer | Name, per-vertex weights per influence, mask, strength, enabled flag | Stacked in a Skin Binding; layers combine in order to give final weights |
| Weight Map | Per-vertex scalar values, name | Used as a mask or weight for deformers, layers and simulation |
| Blend Shape Set | Base mesh, target list, deformation order | Holds Shape Targets; driven by rig attributes |
| Shape Target | Point offsets (or delta positions), in-between positions, per-target weight mask, mirror partner | Member of a Blend Shape Set; may be a corrective |
| Pose-Space Deformer | Driver joints or attributes, pose list, interpolation kernel, output targets | Reads rig pose; drives Shape Targets or weight layer offsets |
| Deformer | Type, input geometry, envelope, weight map, bounds, enabled flag | Node in a mesh's deformer chain; evaluated in order |
| Deformer Handle | Transform, falloff, bounds | Controls a lattice, wrap, nonlinear, wire or cluster deformer |
| Collision Deformer | Collider list, thickness, stiffness, falloff | Pushes a mesh out of colliders after other deformers |
| Muscle Object | Shape, attachment points, rest length, volume, stiffness | Attached to Joints; drives surface deformation by bulge and slide |
| Learned Deformer | Model weights, input rig description, training set reference, accuracy report | Replaces a set of deformers on a mesh with a fast approximation |
| Training Set | Pose samples, reference mesh results, metadata | Generated from a rig; used to train and validate a Learned Deformer |

## Binding

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SKIN-001 | Linear blend binding | Binds a mesh to selected joints and computes vertex positions as the weighted sum of influence transforms applied to the bind-pose position. | T1 | 4 |
| MB-SKIN-002 | Dual quaternion binding | Blends influence transforms as dual quaternions to keep volume at twisting joints; the method is a per-binding setting and can be changed without losing weights. | T1 | 4 |
| MB-SKIN-003 | Blended binding | Mixes linear and dual quaternion results per vertex using a weight map so that elbows and shoulders can use different methods. | T2 | 4 |
| MB-SKIN-004 | Rigid binding | Assigns each vertex wholly to its closest influence, for mechanical parts. | T2 | 4 |
| MB-SKIN-005 | Automatic initial weights | Computes starting weights by closest distance, by geodesic distance through the mesh volume, or by diffusion along the surface; the author selects the method and a maximum influence count. | T1 | 4 |
| MB-SKIN-006 | Bind pose management | Stores the bind pose; unbinding can keep or discard weights, and rebinding at a different pose preserves the visible mesh. | T1 | 4 |
| MB-SKIN-007 | Influence list editing | Adds or removes joints and influence objects on an existing binding; removal can redistribute that influence's weights to a chosen influence or to neighbors. | T1 | 4 |
| MB-SKIN-008 | Influence objects | A mesh or curve can act as an influence with its own bind transform, so props, belts and tails deform surfaces without being joints. | T2 | 4 |
| MB-SKIN-009 | Max influences and normalization | A binding limits non-zero influences per vertex (default four, up to sixteen) and normalizes weights on edit, or on demand when set to post-normalization. | T1 | 4 |

## Weight editing

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SKIN-010 | Weight painting | A brush paints influence weights with add, replace, scale and smooth modes, size, strength, falloff, symmetry and a pressure response; the viewport shows the active influence as a color ramp. | T1 | 4 |
| MB-SKIN-011 | Flood and region operations | Applies an operation to the whole mesh, a selection or a mask: set, add, scale, smooth, prune small weights below a threshold, and normalize. | T1 | 4 |
| MB-SKIN-012 | Influence locking | Locked influences keep their weights while others are painted or normalized, and locks are stored in the binding. | T1 | 4 |
| MB-SKIN-013 | Weight layers | Weights are edited in non-destructive layers with a mask and a strength slider; layers combine in order, can be reordered, muted and merged, and the original weights remain recoverable. | T2 | 4 |
| MB-SKIN-014 | Weight mirroring | Mirrors weights across a plane or by topology using joint labels to pair influences, with a tolerance for off-axis vertices and a report listing unmatched vertices. | T1 | 4 |
| MB-SKIN-015 | Component weight table | A spreadsheet view lists selected vertices against influences for typed edits, copy and paste, and sorting by weight. | T1 | 4 |
| MB-SKIN-016 | Weight hammer and smoothing | A repair command replaces the weights of selected vertices by averaging their neighbors and is available as a brush and as a region operation. | T2 | 4 |
| MB-SKIN-017 | Weight import and export | Reads and writes weights per influence as named maps or as a single data file, matching influences by name or label and reporting unmatched ones. | T1 | 4 |

## Weight transfer

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SKIN-018 | Weight transfer between meshes | Copies weights from a bound mesh to another mesh by nearest surface point, nearest vertex, UV or ray projection, with an influence mapping and a blend with existing weights. | T1 | 4 |
| MB-SKIN-019 | Proxy and clothing weight transfer | Transfers weights from a body to clothing or hair meshes by proximity with smoothing and a thickness offset, and optionally keeps the clothing bound to the same skeleton. | T2 | 4 |
| MB-SKIN-020 | Topology-independent rebind | Rebinding after a mesh edit carries weights to the new topology by closest-surface transfer and lists vertices whose transfer error exceeds a threshold. | T2 | 4 |
| MB-SKIN-021 | Weights between skeletons | Maps weights from one skeleton to another through a joint mapping, merging many-to-one cases and reporting unmapped influences. | T3 | 4 |

## Blend shapes

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SKIN-022 | Blend shape set | Creates a set from a base mesh and sculpted target meshes; each target stores point deltas and has a weight attribute that can be keyed or driven. | T1 | 4 |
| MB-SKIN-023 | Shape target editing | A target can be sculpted in place while the set is evaluated, with the base and other shapes visible as references and the edit stored as deltas. | T1 | 4 |
| MB-SKIN-024 | In-between targets | A target holds additional shapes at intermediate weights; evaluation interpolates through them so the path between neutral and full is not a straight line. | T1 | 4 |
| MB-SKIN-025 | Combination targets | A combination target is evaluated as a corrective whose weight is the product of its parent target weights, so extreme combinations stay clean. | T1 | 4 |
| MB-SKIN-026 | Per-target weight masks | Each target can carry a vertex weight map that limits where it applies, allowing one shape to be split into regions without separate meshes. | T1 | 4 |
| MB-SKIN-027 | Target mirror and flip | A target is mirrored or flipped across the symmetry plane to produce its opposite, using topological symmetry when the mesh is asymmetric. | T1 | 4 |
| MB-SKIN-028 | Target extraction | A shape reached by posing or deforming a mesh can be extracted as a target relative to a chosen base, including from skinned poses. | T2 | 4 |
| MB-SKIN-029 | Blend shape ordering | A set can be placed before or after the skin binding in the deformer chain, or evaluated in parallel with it, with the effect on tangent and normal spaces stated in the set. | T1 | 4 |
| MB-SKIN-030 | Set transfer between meshes | Blend shape sets move to another mesh with the same or different topology by wrap or surface transfer, producing new targets on the destination. | T2 | 4 |

## Pose-space and corrective shapes

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SKIN-031 | Pose-space deformer | Stores sculpted corrections at sampled joint poses and interpolates them with a radial basis kernel as the rig moves between samples; the output drives corrective targets or weight layer offsets. | T1 | 4 |
| MB-SKIN-032 | Pose sampling | Poses are captured from the current rig state with their driver values, and a distance map shows how far the current pose is from each stored sample. | T1 | 4 |
| MB-SKIN-033 | Corrective from pose | A command creates a corrective target at the current pose by taking the difference between the sculpted result and the skinned result, ready for sculpting. | T1 | 4 |
| MB-SKIN-034 | Driver types | A pose-space deformer reads joint rotations, swing and twist components, or arbitrary attributes, with a choice of kernel and a regularization setting. | T2 | 4 |

## Shape deformers

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SKIN-035 | Lattice deformer | Deforms geometry through a regular lattice with divisions per axis, local or global influence and a weight map; moving lattice points moves the enclosed geometry. | T1 | 4 |
| MB-SKIN-036 | Wrap deformer | Makes a mesh follow another mesh or cage by binding each vertex to nearby driver surface points, with controls for influence distance, falloff and an inflate option. | T1 | 4 |
| MB-SKIN-037 | Cluster deformer | A handle moves a weighted group of points; the weights come from a map and the handle can be parented, constrained and animated. | T1 | 4 |
| MB-SKIN-038 | Wire deformer | Deforms geometry by moving a curve, with dropoff distance, per-point weights and optional secondary wrinkle curves. | T2 | 4 |
| MB-SKIN-039 | Bend, twist and taper deformers | Axis-based deformers with a handle transform, lower and upper bounds, curvature or angle parameters, and a weight map. | T1 | 4 |
| MB-SKIN-040 | Wave and ripple deformers | Procedural wave and ripple displacement with amplitude, wavelength, offset, direction and bounds, driven by time or an attribute. | T3 | 4 |
| MB-SKIN-041 | Soft modification | Moves a point and its neighbors with a falloff curve using a handle that can be keyed; the falloff mode may be volume or surface distance. | T2 | 4 |
| MB-SKIN-042 | Shrink-to-surface deformer | Projects a mesh onto a target surface along normals, closest points or a direction, with an offset and a blend weight. | T2 | 4 |

## Smoothing and tension

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SKIN-043 | Detail-preserving smoothing | Smooths a deformed mesh and then restores the local surface detail of the rest shape, which removes skinning pinch artifacts without flattening fine features; iterations, strength and a weight map are parameters. | T1 | 4 |
| MB-SKIN-044 | Tension smoothing | Relaxes regions where the deformed surface is stretched or compressed relative to rest, with separate strengths for stretch and compression and a weight map. | T2 | 4 |
| MB-SKIN-045 | Skin sliding | A deformer lets skin slide over underlying bones or muscles by projecting vertices onto an inner surface, with a stiffness weight map. | T3 | 5 |

## Muscle and collision deformation

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SKIN-046 | Muscle objects | A muscle is a shape with attachment points on joints that bulges as its length shortens, keeping volume within limits, and pushes the surface through a weight map. | T3 | 5 |
| MB-SKIN-047 | Jiggle deformer | Adds secondary motion to a mesh or a point set from motion with stiffness, damping and a weight map; results are deterministic and cached with the animation. | T2 | 5 |
| MB-SKIN-048 | Collision deformation | Pushes a deformed mesh out of collider meshes with a thickness and a falloff, so skin, cloth proxies and armor avoid interpenetration without a simulation. | T2 | 5 |

## Deformer chain and evaluation

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SKIN-049 | Deformer chain | Deformers on a mesh form an ordered list that can be reordered, disabled, given an envelope, and inspected; reordering shows its effect immediately and records no destructive change. | T1 | 4 |
| MB-SKIN-050 | Deformer weight maps | Any deformer can read a Weight Map as a per-vertex strength, and maps are painted, mirrored and transferred with the same tools as skin weights. | T1 | 4 |
| MB-SKIN-051 | Parallel and accelerated evaluation | Deformers declare whether they are parallel-safe and GPU-eligible; eligible chains evaluate across meshes and vertices in parallel, and the evaluation profile reports any node that forces serial evaluation. | T1 | 4 |
| MB-SKIN-052 | Normal and tangent update | Deformed normals and tangents are recomputed after the chain with a selectable method (analytic, recalculated from faces), so shading and baking see correct data. | T1 | 4 |

## Learned deformers

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SKIN-053 | Training set generation | Samples the rig across its pose range, solves the full deformer chain at each sample and stores the inputs and resulting meshes with a sampling report that shows coverage of the pose space. | T3 | 5 |
| MB-SKIN-054 | Learned deformer training | Trains a model on a training set to approximate the rig's deformation on one mesh, using a training procedure that runs locally and on Hub workers, with progress and cancel. | T3 | 5 |
| MB-SKIN-055 | Learned deformer evaluation | A trained model replaces the heavy deformer chain at playback time, evaluates on the CPU or GPU, and can be switched back to the original chain per mesh. | T3 | 5 |
| MB-SKIN-056 | Learned deformer accuracy report | A validation pass compares the model against held-out poses and reports maximum and average vertex error and the worst poses, and the model refuses to activate above a configurable error limit. | T3 | 5 |
