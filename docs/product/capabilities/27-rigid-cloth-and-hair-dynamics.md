# Rigid, cloth and hair dynamics

This area specifies the simulation of rigid bodies and their constraints, soft bodies, cloth, and hair and fur, together with grooming, forces and fields, caching and solver settings. Simulations are nodes in the procedural graph evaluated by a deterministic solver with revision-tagged results, so a cached simulation is reproducible, can be scrubbed, and is never shown for a document state it was not computed from.

## Domain objects

| Object | Key properties | Relationships |
|---|---|---|
| Solver | Type, substeps, iterations, gravity, time scale, scale, seed, determinism mode | Owns the bodies, cloth and hair systems assigned to it; reads Fields and Colliders |
| Rigid Body | Mass, center of mass, friction, restitution, damping, mode (dynamic, kinematic, static), shape | Belongs to a Solver; has a Collision Shape; may be driven by animation |
| Collision Shape | Type (box, sphere, capsule, convex hull, mesh, compound), margin, offset | Attached to a Rigid Body or Collider |
| Rigid Constraint | Type (fixed, hinge, slider, ball, cone-twist, spring), anchors, limits, breaking threshold | Connects two Rigid Bodies or a body and the world |
| Soft Body | Source mesh, stiffness, damping, pressure, goal weights, volume preservation | Belongs to a Solver; deforms a mesh |
| Cloth | Source mesh, material parameters (stretch, shear, bend, density), thickness, self-collision flag, constraints | Belongs to a Solver; collides with Colliders |
| Cloth Constraint | Type (pin, attach to surface, glue, tear threshold, pressure), targets | Applies to cloth vertices or regions |
| Garment Panel | 2D outline, seam list, fabric preset | Seamed into a 3D Cloth mesh through draping |
| Hair System | Emitter surface, Guide Curves, density, interpolation, clump maps, material references | Rendered as curves; simulated by Hair Solver |
| Guide Curve | Control points, root attachment, length, stiffness | Drives interpolated strands |
| Groom Modifier | Type (clump, noise, frizz, cut, curl, flyaway, braid), parameters, weight map | Stack on a Hair System |
| Groom Brush | Type (comb, length, puff, smooth, cut, pinch, add, grow), size, strength | Interactive edit of Guide Curves |
| Force Field | Type, strength, falloff, bounds, noise, direction | Affects any simulation object within bounds |
| Collider | Source mesh or shape, friction, thickness, animated flag | Collides with cloth, hair, soft and rigid bodies |
| Simulation Cache | Solver reference, frame range, data layout, revision, hash of inputs | Stores per-frame results; invalid when inputs change |

## Rigid bodies and constraints

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-DYN-001 | Rigid body simulation | Simulates dynamic, kinematic and static bodies with mass, friction, restitution and damping, and writes transforms back to scene objects at each frame. | T1 | 5 |
| MB-DYN-002 | Collision shapes | Bodies use box, sphere, capsule, convex hull, mesh and compound shapes with margins; convex hulls and compound decompositions are generated automatically from meshes with an accuracy setting. | T1 | 5 |
| MB-DYN-003 | Mass properties | Mass and center of mass derive from shape and density or from explicit values, and a display shows the resulting inertia. | T1 | 5 |
| MB-DYN-004 | Rigid constraints | Fixed, hinge, slider, ball, cone-twist and spring constraints connect bodies with anchors, limits and optional motors, and breaking thresholds disconnect them on overload. | T1 | 5 |
| MB-DYN-005 | Animation-driven bodies | A body switches between animated and dynamic over time, inheriting velocity from the animation at release, with a keyable switch. | T1 | 5 |
| MB-DYN-006 | Ragdoll | A ragdoll is generated from a skeleton with shapes and cone-twist constraints from joint limits, and blends with animation by a weight over time. | T2 | 5 |
| MB-DYN-007 | Fracture and breakage | A mesh splits into pieces by a pattern (voronoi, planar, texture-driven) with inside faces generated, and the pieces join through breakable constraints in a simulation. | T2 | 5 |
| MB-DYN-008 | Simulation to keys | A rigid simulation bakes to keys on body objects over a range, with simplification options, so results are editable and exportable. | T1 | 5 |
| MB-DYN-009 | Many-body performance | Simulations of tens of thousands of bodies run on worker threads with a broad phase suited to large counts, and the solver reports the step time and body count. | T2 | 5 |
| MB-DYN-010 | Rigid body sleeping and stability | Bodies at rest sleep after a threshold and wake on contact; stack stability settings (iterations, solver type, contact tolerance) are on the Solver. | T1 | 5 |

## Soft bodies

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-DYN-011 | Soft body simulation | Simulates a mesh as a deformable body using a volume or surface model with stiffness, damping, pressure and volume preservation parameters. | T1 | 5 |
| MB-DYN-012 | Goal weights | A weight map defines how strongly each vertex follows its animated position, so parts of a body stay rigid and others wobble. | T1 | 5 |
| MB-DYN-013 | Soft body collisions | Soft bodies collide with colliders and with themselves, with friction and thickness, and can push rigid bodies through coupling. | T2 | 5 |
| MB-DYN-014 | Soft body as deformer | A soft body result can drive a high-resolution mesh through a low-resolution proxy, so detailed skin follows a coarse simulation. | T2 | 5 |

## Cloth

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-DYN-015 | Cloth simulation | Simulates a triangle or quad mesh as cloth using stretch, shear and bend stiffness, density, damping and thickness, with stable behavior at the solver's substep and iteration settings. | T1 | 5 |
| MB-DYN-016 | Fabric presets | Presets set material parameters for common fabrics, and a parameter set can be saved, shared and blended across a mesh with a weight map. | T1 | 5 |
| MB-DYN-017 | Cloth collisions | Cloth collides with animated and deforming colliders with thickness and friction, continuous detection for fast motion, and a collision quality setting. | T1 | 5 |
| MB-DYN-018 | Self-collision | Cloth collides with itself with a thickness and layering setting, and a check reports remaining interpenetration after a step. | T1 | 5 |
| MB-DYN-019 | Pinning and attachments | Vertices are pinned to a transform, a bone or a surface point, with animatable strength, and a region can be glued to another cloth or to a collider. | T1 | 5 |
| MB-DYN-020 | Tearing and pressure | Cloth tears when strain exceeds a threshold along defined edges or across the mesh, and a pressure value inflates closed meshes. | T2 | 5 |
| MB-DYN-021 | Pattern-based garments | A garment is authored from 2D panels with seams and fabric presets; a drape command sews and drapes the panels onto a body, and the result is a cloth mesh with UVs from the panels. | T2 | 5 |
| MB-DYN-022 | Cloth to character | A simulated garment follows a posed character via collision and is given a start pose from a relaxed stage that settles before the animation range. | T1 | 5 |
| MB-DYN-023 | Cloth detail transfer | A low-resolution cloth simulation can drive a high-resolution mesh by a binding that preserves wrinkles from a detail layer. | T2 | 5 |
| MB-DYN-024 | Wind and cloth forces | Cloth responds to wind, drag, lift and turbulence fields with per-fabric aerodynamic coefficients. | T2 | 5 |

## Hair and fur grooming

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-DYN-025 | Hair system | A hair system grows guide curves from an emitter surface, with density, per-region length and direction from maps, and interpolates a large strand count from the guides at display and render time. | T1 | 5 |
| MB-DYN-026 | Guide editing | Guides are created, extended and edited interactively with control points; edits affect nearby interpolated strands, and a viewport shows both guides and a preview of strands. | T1 | 5 |
| MB-DYN-027 | Groom brushes | Brushes comb, push, pull, puff, smooth, cut, grow, add and delete guides with size, strength and falloff, and symmetry and a collision-aware mode keep hair out of the skin. | T1 | 5 |
| MB-DYN-028 | Groom modifier stack | Ordered modifiers shape strands (clump, noise, frizz, curl, wave, cut, flyaway, braid, spread) with weight maps, and the stack is non-destructive and reorderable. | T1 | 5 |
| MB-DYN-029 | Clumping | Strands gather into clumps by a clump map or by procedurally placed clump centers with count, size, tightness and tip shape controls. | T1 | 5 |
| MB-DYN-030 | Strand attributes | Strand width, color, roughness and melanin-style color parameters vary along the strand and across the emitter by maps and ramps, and are passed to RenderBuilder. | T1 | 5 |
| MB-DYN-031 | Hair from curves and cards | A groom is converted to hair cards or polygon strips for real-time use, with texture atlases baked from the strand groom. | T2 | 5 |
| MB-DYN-032 | Fur and instancing | Fur mode places very many short strands with level-of-detail rules, and instanced geometry (for example grass or feathers) uses the same distribution maps. | T2 | 5 |
| MB-DYN-033 | Groom interchange | Strands export and import as curve data in OpenUSD and Alembic, with width, color and per-strand attributes preserved. | T1 | 5 |

## Hair simulation

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-DYN-034 | Hair solver | Simulates guide curves as strands with stretch, bend and twist stiffness, damping, gravity and root attachment, and drives interpolated strands from the simulated guides. | T1 | 5 |
| MB-DYN-035 | Hair collisions | Hair collides with the body and other colliders with thickness and friction, and a self-collision option keeps strands from passing through each other. | T1 | 5 |
| MB-DYN-036 | Hair-to-hair interaction | A coarse grid or density-based model lets strands push each other, including volume preservation and clump cohesion, at a stated cost. | T2 | 5 |
| MB-DYN-037 | Hair dynamics weights | Stiffness, damping and attachment strength come from weight maps painted on the emitter and along the strand length. | T1 | 5 |
| MB-DYN-038 | Hair with wind and fields | Hair responds to wind, turbulence and fields, and to motion of the head, with shot-to-shot repeatability from the solver's seed. | T1 | 5 |

## Forces and fields

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-DYN-039 | Force fields | Gravity, wind, drag, radial, vortex, turbulence, uniform and volume-axis fields apply forces within bounds and with falloff to any simulation object that opts in. | T1 | 5 |
| MB-DYN-040 | Field masks and noise | A field's strength varies by a mask, a noise function with animation, or a texture, and fields combine by adding or by priority. | T2 | 5 |
| MB-DYN-041 | Field from curve and volume | A field can follow a curve or sample a volume's velocity data, so flow can be authored or taken from another simulation. | T2 | 5 |
| MB-DYN-042 | Field visualization | Fields draw as arrows, streamlines or bounds in the viewport, and a probe tool reads the force at a clicked point. | T2 | 5 |

## Caching

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-DYN-043 | Simulation cache | Each solver writes per-frame results to a cache in the document package or to disk; playback reads the cache, and the cache is marked stale whenever an upstream input changes. | T1 | 5 |
| MB-DYN-044 | Cache scrubbing and restart | The timeline scrubs cached frames in any order; running a simulation from a chosen cached frame continues from that state, and the cache after that frame is invalidated. | T1 | 5 |
| MB-DYN-045 | Cache formats | Caches export and import as Alembic for geometry, OpenUSD for geometry and strands, and as native point caches for particles and cloth, and a cache can replace a live simulation in a shot. | T1 | 5 |
| MB-DYN-046 | Cache management | A panel lists caches with size, frame range, validity and a hash of inputs; caches can be merged, trimmed, relocated and deleted, and Hub jobs can compute a cache on a worker. | T2 | 5 |
| MB-DYN-047 | Deterministic simulation | With determinism enabled, the same inputs and seed give identical results on the same hardware and thread count, and the solver states what is not guaranteed across machines. | T1 | 5 |

## Solver settings

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-DYN-048 | Substeps and iterations | Substep count, solver iterations and collision iterations are set per solver and per object, with an adaptive substep option driven by velocity. | T1 | 5 |
| MB-DYN-049 | Scale and units | Simulation uses a scale setting that relates scene units to physical units, defaulting to meters and kilograms, so material parameters keep their meaning at any scene scale. | T1 | 5 |
| MB-DYN-050 | Start frame and initial state | A solver has a start frame, an initial state from the current pose or a cache, and a warm-up range before the animation range. | T1 | 5 |
| MB-DYN-051 | Solver profile | The profile reports per-frame step time, collision count, constraint residuals and memory, and highlights objects that dominate cost. | T2 | 5 |
| MB-DYN-052 | Simulation graph integration | Solvers, bodies, cloth, hair and fields are nodes in the procedural graph, so simulation inputs and results can be wired to modeling, rigging and motion graphics nodes. | T1 | 5 |
