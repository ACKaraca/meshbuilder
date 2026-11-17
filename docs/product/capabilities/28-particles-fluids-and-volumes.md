# Particles, fluids and volumes

This area specifies particle systems, event-driven particle behavior, instancing on particles, liquid, smoke, fire and granular simulation, ocean surfaces, volume data and its operations, meshing of simulation results, and cache formats. All of it is built from nodes in the shared procedural graph, so a particle emitter, a volume operation and a mesher use the same editor, caching and parallel evaluation as modeling and rigging.

## Domain objects

| Object | Key properties | Relationships |
|---|---|---|
| Particle System | Point set with attributes (position, velocity, age, life, mass, orientation, scale, color, ID), seed, solver settings | Created by Emitters; changed by Events, Forces and Colliders; read by Instancers and Meshers |
| Emitter | Source (point, line, surface, volume, mesh, texture), rate or count, initial attributes, start and end time, seed | Adds points to a Particle System |
| Event | Name, entry condition (test), actions, order | Part of a Particle System's flow; passes particles to other Events |
| Rule | Operator or test (birth, speed, force, collide, spawn, delete, age, split, property) with parameters | Used inside Events |
| Instance Source | Geometry, material overrides, variants, animation clip | Referenced by an Instancer driven by points |
| Fluid Domain | Type (liquid, gas, granular), bounds, resolution, voxel size, adaptive settings, solver parameters | Contains Emitters, Colliders, Fields; produces Volume Grids and Particle sets |
| Liquid Parameters | Viscosity, surface tension, density, foam and spray settings, FLIP ratio | Part of a liquid Fluid Domain |
| Combustion Parameters | Fuel, temperature, burn rate, expansion, buoyancy, smoke generation, color ramps | Part of a gas Fluid Domain |
| Granular Parameters | Material model (sand, snow, soil, elastic), friction angle, cohesion, hardening | Part of a granular Fluid Domain |
| Ocean Surface | Spectrum, wind, depth, resolution, size, choppiness, foam rules, time offset | Evaluated as a displacement field or mesh; emits foam and spray |
| Volume Grid | Name, voxel size, transform, data type (density, temperature, velocity, level set), background value, bounds | Sparse volume data; output of simulation and volume operations |
| Volume Operation | Type, input grids, parameters | Node transforming Volume Grids |
| Mesher | Input (particles or level set), voxel size, smoothing, adaptivity, attribute transfer | Converts simulation data to polygon mesh |
| FX Cache | Source node, frame range, format, revision, input hash | Stores per-frame results; stale when inputs change |

## Particle emitters

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-FX-001 | Particle system | A particle system is a set of points with named attributes, advanced per frame by forces, collisions and events; the same system can be read by modeling, rigging and instancing nodes. | T1 | 5 |
| MB-FX-002 | Emitters | Emit from points, curves, mesh surfaces, volumes, texture-weighted regions and animated transforms, by rate or count, with initial velocity, spread, speed variation and lifetime. | T1 | 5 |
| MB-FX-003 | Emission from animation | Emitters inherit velocity from the emitting object, from a skinned surface, or from a velocity field, so particles shed motion from the source. | T1 | 5 |
| MB-FX-004 | Particle attributes | Standard and custom attributes (scalar, vector, integer, color, string) are added, read and written by nodes and expressions, and they travel with instances and caches. | T1 | 5 |
| MB-FX-005 | Particle forces and fields | Points respond to gravity, drag, wind, turbulence, vortex, radial and volume fields (see MB-DYN) with per-particle mass and drag coefficients. | T1 | 5 |
| MB-FX-006 | Particle collisions | Particles collide with meshes and volumes, with bounce, friction, stick, slide and kill outcomes and a collision event hook. | T1 | 5 |
| MB-FX-007 | Particle interaction | Particles repel, attract or flock through a neighbor search with radius and strength, and a limit on neighbors keeps the cost bounded. | T2 | 5 |
| MB-FX-008 | Deterministic seeding | Every emitter and random node has a seed, and a particle's random values derive from its ID so changing emission count does not reshuffle earlier particles. | T1 | 5 |

## Events and rules

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-FX-009 | Events | A particle system's behavior is organized in events that hold a list of rules; a particle is in one event at a time and moves between events when a test passes. | T1 | 5 |
| MB-FX-010 | Tests | Tests include age, collision, speed, distance, property, split by amount or by probability, and spatial region, and each test sends passing particles to a chosen event. | T1 | 5 |
| MB-FX-011 | Operators | Rules set position, speed, rotation, shape, scale, color and material, add force, spawn new particles, and delete particles, with per-rule weight maps and ramps. | T1 | 5 |
| MB-FX-012 | Spawn and split | Spawning creates child particles with inherited and new attributes, in counts and rates; splitting divides a particle into several. | T1 | 5 |
| MB-FX-013 | Particle graph editing | Events and rules are shown and edited as nodes in the graph editor and as an ordered list, and both views edit the same data. | T1 | 5 |
| MB-FX-014 | Rule libraries | Rule sets are saved as reusable assets (explosion debris, rain, sparks, dust, confetti) and shared through project libraries. | T2 | 5 |

## Instancing

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-FX-015 | Instance geometry on particles | A node places geometry at each particle with orientation from velocity, a normal or an attribute, and scale from an attribute or random range, and instances share source meshes. | T1 | 5 |
| MB-FX-016 | Instance variants and animation | An instancer chooses among several source objects by weight or attribute and offsets animation clips per instance by age or random seed. | T1 | 5 |
| MB-FX-017 | Instance output | Instances remain instances in OpenUSD export and in the Arc Scene sent to RenderBuilder; a command converts them to real geometry when needed. | T1 | 5 |
| MB-FX-018 | Instance selection and edit | Individual instances can be selected, hidden and overridden in material or transform, with overrides stored as attributes on points. | T2 | 5 |
| MB-FX-019 | Instancing at scale | Millions of instances display in the viewport using GPU instancing with level-of-detail proxies and culling, with the instance count and draw cost reported. | T1 | 5 |

## Liquid simulation

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-FX-020 | Liquid solver | Simulates liquids with a particle-and-grid method, parameters for viscosity, surface tension, density and gravity, and a domain with bounds and resolution. | T1 | 5 |
| MB-FX-021 | Liquid emitters and colliders | Liquid is emitted from geometry as sources, inflow or initial fill, and collides with animated and static geometry with friction and slip options. | T1 | 5 |
| MB-FX-022 | Foam, spray and splash | Secondary particles for foam, spray and splashes are generated from the liquid's velocity, curvature and surface features with lifetime rules and counts, and are cached separately. | T1 | 5 |
| MB-FX-023 | Adaptive resolution | The solver refines resolution near the surface and coarsens in the interior or far from action, and the domain grows with the liquid, to keep memory bounded. | T2 | 5 |
| MB-FX-024 | Guided liquid | A coarse simulation can guide a fine simulation, so a result is directed by an authored low-resolution motion while detail comes from the solver. | T3 | 5 |
| MB-FX-025 | Liquid coupling | Liquids push rigid bodies and soft bodies and are pushed by them, with a coupling strength and a mass ratio. | T2 | 5 |

## Smoke, fire and combustion

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-FX-026 | Gas solver | Simulates smoke and hot gas on a sparse grid with density, temperature, velocity, buoyancy, dissipation, turbulence and vorticity confinement, with a domain and resolution. | T1 | 5 |
| MB-FX-027 | Fire and combustion | Fuel and temperature fields burn at a rate and produce expansion, heat and smoke, with color driven by temperature ramps, and an emission from burning geometry. | T1 | 5 |
| MB-FX-028 | Explosions | A pressure and expansion source produces a blast with shock, debris coupling and a controllable ignition and fuel setup. | T2 | 5 |
| MB-FX-029 | Gas sources and sinks | Sources add density, temperature, fuel and velocity from geometry, textures or particles; sinks remove them in regions. | T1 | 5 |
| MB-FX-030 | Gas collisions and boundaries | Gas flows around animated colliders with free-slip or no-slip boundaries, and the domain boundaries are open, closed or periodic. | T1 | 5 |
| MB-FX-031 | Volume lighting preview | Gas fields preview in the viewport with shaded density, temperature color and shadowing, and render in RenderBuilder as volume shaders reading named grids. | T1 | 5 |

## Granular and snow-like materials

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-FX-032 | Granular solver | Simulates sand, soil, snow and similar materials with a particle-and-grid continuum method, friction angle, cohesion and hardening parameters, and presets for common materials. | T2 | 5 |
| MB-FX-033 | Elastic and plastic solids | The same solver handles elastic, plastic and fracturing solids with Young's modulus, Poisson's ratio and yield parameters. | T3 | 5 |
| MB-FX-034 | Granular collisions and coupling | Granular material interacts with colliders and rigid bodies, producing footprints, piles and excavation, with friction and compaction. | T2 | 5 |
| MB-FX-035 | Granular surface output | Results output as particles for meshing or instancing and as level sets for direct surface rendering. | T2 | 5 |

## Ocean surfaces

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-FX-036 | Ocean surface | Evaluates an ocean height and displacement field from a wave spectrum, wind, depth and size, tiled and animated with a time offset, as a mesh, a displacement map or a field query. | T2 | 5 |
| MB-FX-037 | Ocean foam and spray | Foam accumulates where the surface folds and decays over time, and crest particles are emitted from foam areas, as grids and particles. | T2 | 5 |
| MB-FX-038 | Ocean interaction | Floating bodies sample the surface for buoyancy and wake emitters add disturbance, and the ocean can blend into a local liquid simulation at a boundary. | T3 | 5 |
| MB-FX-039 | Ocean level of detail | The surface is displayed and evaluated at a resolution that adapts to the camera distance, with a limit on triangle count. | T2 | 5 |

## Volume data and operations

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-FX-040 | Volume grids | Sparse volume grids store scalar, vector and level-set data with a voxel size and transform; they are viewed as slices, isosurfaces or shaded volumes. | T1 | 5 |
| MB-FX-041 | Volume operations | Nodes combine, resample, blur, dilate, erode, advect, remap, clip, transform and merge grids, and convert between mesh, points, level set and density. | T1 | 5 |
| MB-FX-042 | Volume booleans | Union, difference and intersection operate on level sets, with a result convertible to a polygon mesh at a chosen voxel size. | T1 | 5 |
| MB-FX-043 | Volume from geometry | Meshes, curves and point sets convert to density or level-set volumes with a voxel size, band width and thickness. | T1 | 5 |
| MB-FX-044 | Volume sampling and fields | A grid can be sampled at points, used as a force or mask for other simulations, and read by shaders. | T1 | 5 |
| MB-FX-045 | Volume noise and sculpt | Noise, curl-noise advection and a volume brush add detail and shape to grids, with masks and falloff. | T2 | 5 |
| MB-FX-046 | Volume file import and export | Volume grids import and export as OpenVDB with named grids, transforms and metadata, and the import reports unsupported grid types. | T1 | 5 |

## Meshing of simulations

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-FX-047 | Particle meshing | Converts liquid or granular particles to a polygon surface through a level set with voxel size, smoothing, shrink, hole filling and an option to preserve velocity and other attributes. | T1 | 5 |
| MB-FX-048 | Temporal meshing consistency | A meshing option stabilizes the surface across frames to reduce flicker, and records its parameters in the cache. | T2 | 5 |
| MB-FX-049 | Adaptive and tiled meshing | Large domains are meshed in tiles or adaptively with a polygon budget, with seams matched across tile borders. | T2 | 5 |
| MB-FX-050 | Motion data on meshes | A meshed surface carries velocity or per-vertex motion for motion blur in the renderer. | T1 | 5 |

## Caches, formats and performance

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-FX-051 | FX cache | Particles, grids and meshes are cached per frame by their node, scrubbed in any order, resumed from any frame, and marked stale when an upstream input changes. | T1 | 5 |
| MB-FX-052 | Cache formats | Caches read and write OpenVDB for volumes, Alembic and OpenUSD for geometry and points, and a native format for full solver state to continue a simulation. | T1 | 5 |
| MB-FX-053 | Cache compression and streaming | Cache data is compressed by frame and channel with an optional precision reduction, streamed during playback and prefetched ahead of the playhead. | T2 | 5 |
| MB-FX-054 | Parallel and GPU solving | Solvers run across CPU threads and, where implemented, on any supported GPU through the same graph, with identical results within documented tolerances. | T2 | 5 |
| MB-FX-055 | Distributed simulation | A simulation can be split into time or spatial chunks and computed on Hub workers with results merged into one cache. | T3 | 5 |
