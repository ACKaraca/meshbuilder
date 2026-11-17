# Motion graphics and crowds

This area specifies procedural motion graphics and crowd simulation in MeshBuilder: instancing networks with distribution, effectors and falloffs, text and spline animation, trails and connection effects, and crowd agents with behaviors and navigation. These are built on the same procedural node graph as modeling, rigging and simulation, so motion graphics, particles and crowds can drive and read each other.

## Domain objects

| Object | Key properties | Relationships |
|---|---|---|
| Instance Network | Source geometry list, point set, per-instance attributes, instance count, seed | Output of a Distribute node; modified by Effectors; rendered as instances |
| Distribute Node | Mode (linear, grid, radial, mesh surface, mesh volume, curve, point cloud, initial state), count, spacing, seed | Creates the point set of an Instance Network |
| Effector | Type, transform offsets, strength, falloff, per-channel enable, time or audio source | Node on an Instance Network; changes position, rotation, scale, color, visibility or custom attributes |
| Falloff | Shape (box, sphere, linear, cylinder, spline, mesh, noise, texture, audio), curve, invert, animation | Modulates an Effector's strength by position |
| Field | Scalar or vector field from falloffs and noise, combined by layers | Reused by Effectors, simulations and shaders |
| Time Source | Frame range, speed, offset, random offset, delay | Drives time-based Effectors and clip offsets |
| Audio Source | Audio file or stream, frequency bands, gain, smoothing | Provides band values to Effectors and Falloffs |
| Text Object | String, font, size, spacing, extrusion, bevel, per-character attributes | Produces geometry or points per character, word, line |
| Spline Animation | Source Curve, drawing parameter, growth, offset, sweep profile | Animates a curve's length, offset or growth |
| Trail | Source points or objects, length, sampling, width, taper | Generates curve or ribbon geometry along motion |
| Connection Network | Point sets, connection rule (nearest, distance, group), line geometry | Builds lines or curves between points |
| Crowd Agent | Skeleton, geometry variants, clip set, attributes, behavior set, state | Member of a Crowd Population |
| Behavior | Rule graph (state machine or behavior tree) with sensors and actions | Drives an Agent's state, goal and clip selection |
| Navigation Mesh | Walkable polygons, obstacles, costs, off-mesh links | Provides paths for agents |
| Crowd Population | Agent type list, density, spawn regions, seed, simulation parameters | Places and simulates Agents; caches results |

## Procedural instancing networks

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOGRAPH-001 | Instance network | A network creates and transforms many instances of one or more source objects from a point set, shows per-instance attributes, and renders the result as instances. | T1 | 5 |
| MB-MOGRAPH-002 | Distribute modes | Distribution places points in a line, grid, radial pattern, on a mesh surface or volume, along a curve, from a point cloud or particle system, or from an initial state of selected objects, with count and spacing parameters. | T1 | 5 |
| MB-MOGRAPH-003 | Source selection and variants | An instance network chooses among several source objects by weight, by attribute or by order, and keeps variation stable when counts change. | T1 | 5 |
| MB-MOGRAPH-004 | Offset effector | Adds position, rotation, scale and color offsets to instances, modulated by a falloff and a strength, in local, world or per-instance space. | T1 | 5 |
| MB-MOGRAPH-005 | Randomise effector | Randomizes any attribute within ranges using a seed, in uniform, gaussian or discrete distributions, with per-instance stability. | T1 | 5 |
| MB-MOGRAPH-006 | Noise effector | Applies animated noise to attributes with a scale, frequency, speed, octaves and seed. | T1 | 5 |
| MB-MOGRAPH-007 | Time effector | Offsets each instance's time by a function of its index, position or a falloff, so animated sources play with delays, staggers and ripples. | T1 | 5 |
| MB-MOGRAPH-008 | Step and delay effectors | Instances change attributes in turn (step by index) or follow a source with a lag (delay), with curve and mode settings. | T2 | 5 |
| MB-MOGRAPH-009 | Audio effector | Drives attributes from audio frequency band amplitudes with gain, smoothing and mapping curves, so instances react to music; offline analysis is cached. | T1 | 5 |
| MB-MOGRAPH-010 | Shader and image effector | Samples an image or procedural shader at each instance's position or UV to drive scale, color, height and visibility. | T2 | 5 |
| MB-MOGRAPH-011 | Dynamics effector | Instances collide and interact through the rigid body solver, with a toggle per instance for animated or dynamic behavior, and the result is cacheable. | T2 | 5 |
| MB-MOGRAPH-012 | Spring and follow effectors | Instances follow a target with spring dynamics, or follow another object's path with offset by index. | T2 | 5 |
| MB-MOGRAPH-013 | Effector stack | Effectors are ordered on a network, can be enabled, muted and soloed, blend by a strength and a blend mode, and are shown as a stack and as nodes. | T1 | 5 |
| MB-MOGRAPH-014 | Custom effector | A node graph defines an effector from attribute math and sampling nodes, and is saved and shared as an asset. | T1 | 5 |
| MB-MOGRAPH-015 | Instance merge and bake | A network can be merged to a single mesh, baked to separate objects or baked to keys, and the original network remains available. | T1 | 5 |
| MB-MOGRAPH-016 | Network visibility and picking | Individual instances can be hidden or selected by attribute, ID or falloff, with a preview that highlights the selection in the viewport. | T2 | 5 |

## Falloffs and fields

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOGRAPH-017 | Falloff shapes | Falloffs use box, sphere, cylinder, linear, radial, spline, mesh volume, texture and noise shapes with a curve for the edge, an invert option, and a transform that can be animated. | T1 | 5 |
| MB-MOGRAPH-018 | Layered fields | Falloffs combine in layers with add, multiply, minimum, maximum, difference and blend modes, and a layer stack produces one field. | T1 | 5 |
| MB-MOGRAPH-019 | Field remapping | A field's output passes through a remap curve, clamp and offset, and may be quantized to produce stepped transitions. | T2 | 5 |
| MB-MOGRAPH-020 | Fields as shared assets | A field drives effectors, simulation forces, paint masks, deformer weights and materials, and is edited in one place with live preview colors on affected geometry. | T1 | 5 |
| MB-MOGRAPH-021 | Audio falloffs | A falloff's size or strength follows audio bands, so reactive shapes spread through the network. | T2 | 5 |

## Text and spline animation

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOGRAPH-022 | Text objects | Creates text geometry from strings and fonts with size, spacing, alignment, extrusion and bevel, and shapes text correctly for left-to-right and right-to-left scripts. | T1 | 5 |
| MB-MOGRAPH-023 | Per-character instancing | Text can be split into characters, words or lines as points in an instance network, so effectors act on each unit with stable per-unit IDs. | T1 | 5 |
| MB-MOGRAPH-024 | Text animators | Text units are animated by range selectors on position, scale, rotation, opacity and color, with offset and ease controls and expressions. | T2 | 5 |
| MB-MOGRAPH-025 | Spline draw-on | A curve's visible portion is animated by start, end and offset parameters with ease, so lines draw on and off. | T1 | 5 |
| MB-MOGRAPH-026 | Spline growth and flow | Curves grow from seeds with rules for direction, branching and avoidance, or flow along a field, producing animated vines and flow lines. | T3 | 5 |
| MB-MOGRAPH-027 | Sweep along animated splines | A profile sweeps along an animated spline with thickness, taper and twist, producing live geometry that follows the curve. | T1 | 5 |
| MB-MOGRAPH-028 | Vector artwork import | Vector shapes from SVG and PDF import as curves with fills as faces, grouped by layer, ready for extrusion and animation. | T2 | 5 |

## Trails and connection effects

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOGRAPH-029 | Trails | Generates curves or ribbons following the paths of moving objects, points or instances over a time window, with sampling, width, taper and fade attributes. | T1 | 5 |
| MB-MOGRAPH-030 | Connection lines | Builds lines between nearby points of a network or between groups by distance, nearest neighbors or a rule, with maximum count and thickness by distance. | T1 | 5 |
| MB-MOGRAPH-031 | Dynamic connections | Connections follow instance motion live, and can be routed along a surface or spline instead of straight lines. | T3 | 5 |
| MB-MOGRAPH-032 | Metaball-style surfaces | A level-set surface is generated around points with a radius and blend, producing blobby shapes that merge and split with motion. | T2 | 5 |

## Crowd agents, behaviors and navigation

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOGRAPH-033 | Agent definition | An agent type combines a skeleton, geometry variants, a clip set and attributes (height, speed, color), and is created from a rigged character and its clips. | T1 | 5 |
| MB-MOGRAPH-034 | Agent variation | Geometry variants, material parameters, scale and clip offsets vary per agent from distributions and seeds, with controls to avoid duplicates next to each other. | T1 | 5 |
| MB-MOGRAPH-035 | Agent clip sets | Agents select, blend and loop clips by state and speed, with transitions through the nonlinear animation tools, and motion matched to velocity to avoid foot sliding. | T1 | 5 |
| MB-MOGRAPH-036 | Behaviors | Agents follow rule graphs of states, sensors (vision, proximity, ground, group) and actions (walk to goal, flee, follow, idle, play clip), authored as a node graph. | T1 | 5 |
| MB-MOGRAPH-037 | Goals and flow fields | Agents head to goals, along paths or with a flow field painted or drawn on the ground, with speed ranges per agent and per region. | T1 | 5 |
| MB-MOGRAPH-038 | Collision avoidance | Agents avoid each other and obstacles using a local avoidance method with radius, priority and personal space, and group cohesion options. | T1 | 5 |
| MB-MOGRAPH-039 | Navigation mesh | A navigation mesh is generated from walkable surfaces with agent radius, height and slope, edited with obstacle and cost regions, and updated when geometry changes. | T1 | 5 |
| MB-MOGRAPH-040 | Terrain and stairs | Agents conform to terrain and stairs by foot placement on the ground, with limits on slope and step height, evaluated per frame. | T2 | 5 |
| MB-MOGRAPH-041 | Off-mesh links and interactions | Links define jumps, doors, ladders and seats, with clips triggered at link ends and a queue for contested links. | T3 | 5 |

## Populating scenes

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOGRAPH-042 | Population placement | A population places agents in regions or along paths by density, count or lanes, with per-region agent mixes, and avoids overlaps at spawn. | T1 | 5 |
| MB-MOGRAPH-043 | Crowd simulation | A population simulates agents over time with behaviors, navigation and avoidance on worker threads, with a deterministic seed and a start frame. | T1 | 5 |
| MB-MOGRAPH-044 | Crowd cache and playback | Simulated agent states cache per frame, scrub in any order and evaluate skeletal animation on the GPU with level-of-detail meshes at distance. | T1 | 5 |
| MB-MOGRAPH-045 | Crowd editing | A cached crowd can be edited by agent: moved, retimed, swapped to another clip or replaced by a hero character with a handoff frame. | T2 | 5 |
| MB-MOGRAPH-046 | Crowd output | A crowd exports as instances with skeletal animation to OpenUSD and as baked geometry caches through Alembic, and is sent to RenderBuilder as instances with time offsets. | T1 | 5 |
| MB-MOGRAPH-047 | Crowd statistics and debug | A panel shows agent counts, state distribution and avoidance cost, and a debug view draws paths, goals, sensors and neighbors. | T2 | 5 |
| MB-MOGRAPH-048 | Scene population from architectural models | A population can walk through scenes linked from ArchBuilder or SolidBuilder models using their floor and stair surfaces for the navigation mesh. | T3 | 5 |
