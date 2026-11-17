# Keyframe animation

This area specifies how MeshBuilder records and edits animation over time: keys, curves, tangents, rotation handling, motion visualization, driven keys, baking, path and camera animation, shot sequencing, playback caching, and optional assisted in-betweening and locomotion generation. Animation is data on attributes; every edit is a command, undoable, and available through scripts and the automation protocol.

## Domain objects

| Object | Key properties | Relationships |
|---|---|---|
| Timeline | Frame rate, start, end, playback range, time unit, timecode origin, loop mode | One per scene; shared by all animation; shots may override range |
| Animation Curve | Target attribute, key list, interpolation, pre and post extrapolation, weighted flag | One per animated attribute channel; belongs to an Animation Layer or the base animation |
| Key | Time, value, in and out tangent types, tangent angles and weights, lock flags, breakdown flag | Member of an Animation Curve |
| Marker | Time or range, label, color, note | On the Timeline or a Shot; used for navigation and export |
| Breakdown | Reference to a Key, ratio between neighbors | Keeps its place proportionally when neighbor keys move |
| Rotation Channel Set | Rotate order, Euler or quaternion mode, unwrapped angle state | Wraps three or four curves for one rotation |
| Motion Trail | Target object, frame range, sample step, editable-key flag | Derived display of an object's world path; can edit keys |
| Ghost Setting | Frames before and after, step, color, targets | Display-only overlay of poses at other times |
| Path Animation | Object, curve, parameter, orientation settings | Binds an object to a Curve over time |
| Camera Shot | Camera, start and end frame, offset, handles, name, order, notes | Member of a Sequence; refers to a Camera |
| Sequence | Ordered shots, audio references, frame rate, output range | Edited as a timeline of shots; exports a playblast or edit list |
| Playback Cache | Frame range, memory limit, per-frame validity, content scope | Holds evaluated frames; invalidated by document revision |
| Preview Render | Camera, range, display options, resolution, codec, overlays | Written to a movie or image sequence from the viewport |
| Generated Motion | Archetype, path or goal, style parameters, step events, output layer | Produces keys on a rig's controls; editable like hand-keyed animation |

## Time and playback

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ANIM-001 | Timeline and frame rate | The scene has a frame rate (presets and custom rational values), a playback range and an animation range; time is stored as rational ticks so frame times are exact and fractional frames are legal. | T1 | 4 |
| MB-ANIM-002 | Transport controls | Play, loop, step, jump to keys, jump to markers, playback speed and real-time or every-frame modes; the display shows the achieved frame rate. | T1 | 4 |
| MB-ANIM-003 | Timeline markers | Markers and marker ranges are added at frames with labels and colors, listed in a panel, used for navigation and written to exported files that support them. | T2 | 4 |
| MB-ANIM-004 | Audio on the timeline | Imported audio shows its waveform on the timeline, scrubs with the playhead and plays in sync with playback, with an offset setting and mute. | T1 | 4 |
| MB-ANIM-005 | Animation bookmarks | Named frame ranges are stored for quick selection of the playback range and are available to shot and clip tools. | T2 | 4 |

## Keys and auto-key

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ANIM-006 | Set key | Records keys on selected channels at the current time with filters for translation, rotation, scale, custom attributes and unlocked channels only. | T1 | 4 |
| MB-ANIM-007 | Auto-key | While enabled, changing an attribute creates or updates a key at the current time; the mode keys only already-animated channels or all changed channels. | T1 | 4 |
| MB-ANIM-008 | Key editing | Keys are selected, moved, scaled around a pivot time or value, copied, pasted with offset or merge, deleted, and inserted on a curve without changing its shape. | T1 | 4 |
| MB-ANIM-009 | Key snapping | Key time and value snap to frames, to other keys, to markers or to zero, with the snap chosen per operation. | T2 | 4 |
| MB-ANIM-010 | Stepped preview and blocking | A stepped key mode holds each value until the next key, so poses can be blocked and reviewed before the curves are refined to spline. | T1 | 4 |
| MB-ANIM-011 | Retime and scale time | A selection of keys, a range or the whole animation is retimed by moving, scaling or reversing time, with ripple behavior to open or close gaps. | T1 | 4 |
| MB-ANIM-012 | Breakdowns | A key may be marked as a breakdown that holds a ratio between its neighbors, so moving neighbor keys moves it proportionally; a tween command creates a breakdown at a chosen percentage between the surrounding keys. | T1 | 4 |
| MB-ANIM-013 | Holds | A hold creates a key at the end of a held pose that repeats the previous value with flat tangents, with options for a moving hold that drifts by a small amount. | T2 | 4 |
| MB-ANIM-014 | Copy and paste of poses and keys | Poses and key ranges copy between objects, characters of one rig template and scenes, including mirrored paste across the symmetry plane. | T1 | 4 |

## Tangents and curves

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ANIM-015 | Tangent types | Keys use automatic, clamped, spline, linear, flat, stepped, fixed and plateau tangent types; in and out tangents are set separately and a default type is a user setting. | T1 | 4 |
| MB-ANIM-016 | Weighted tangents | A curve may be weighted, adding a tangent length per side so handles control both angle and influence; converting between weighted and unweighted preserves the shape within a stated tolerance. | T1 | 4 |
| MB-ANIM-017 | Tangent break and unify | Tangents can be broken so in and out handles move independently, unified, or locked in angle or weight. | T1 | 4 |
| MB-ANIM-018 | Extrapolation modes | Before the first key and after the last key a curve holds constant, continues linearly, cycles, cycles with offset, or oscillates. | T1 | 4 |
| MB-ANIM-019 | Curve editor | Shows the function curves of selected objects with a channel tree, key and tangent manipulation, normalized view, stacked view, isolation of a curve, a buffer snapshot to compare against, and soft selection of keys. | T1 | 4 |
| MB-ANIM-020 | Curve tools | Applies smoothing, ease in and out, bias, overshoot removal, curve reverse, and a pair of operations that scale a selection of keys toward or away from the curve's mean. | T2 | 4 |
| MB-ANIM-021 | Dope sheet | Shows keys as marks on rows per object and channel with summary rows, box selection, range retiming and drag across channels; edits are identical to curve editor edits. | T1 | 4 |

## Rotation handling

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ANIM-022 | Euler and quaternion channels | A rotation is animated as three Euler curves in a chosen rotate order or as a quaternion interpolated by shortest path; changing mode converts existing keys and reports the result. | T1 | 4 |
| MB-ANIM-023 | Euler filter | A filter removes flips and unwraps angles across a key range so consecutive keys differ by the smallest equivalent rotation. | T1 | 4 |
| MB-ANIM-024 | Gimbal display | The rotate manipulator can show gimbal axes, and a warning marks keys near a gimbal singularity for the chosen rotate order. | T2 | 4 |

## Motion visualization

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ANIM-025 | Motion trails | Draws an object's world-space path over a frame range with frame ticks and key markers; dragging a key marker edits the underlying key in world space. | T1 | 4 |
| MB-ANIM-026 | Ghosting | Shows semi-transparent copies of selected objects at frames before and after the current time, at a step, or at chosen frames, with colors for before and after. | T1 | 4 |
| MB-ANIM-027 | Trail from path | A motion trail can be converted to a curve and a curve can be applied as a trail, so authored paths and keyed motion exchange. | T3 | 4 |

## Driven keys

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ANIM-028 | Driven keys | An attribute animated from another attribute's value instead of time; keys on a driver-to-driven curve are edited in the curve editor with the driver on the horizontal axis. | T1 | 4 |
| MB-ANIM-029 | Driven key sets | Several driven attributes can share one driver in a named set that is applied, muted or baked as one. | T2 | 4 |

## Bake, simplify and cleanup

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ANIM-030 | Animation bake | Evaluates selected objects over a range at a step and writes keys that reproduce constraints, solvers, drivers and layers, with options to remove the source and to bake in world or local space. | T1 | 4 |
| MB-ANIM-031 | Curve simplify | Reduces the keys of selected curves to the fewest that reproduce the curve within a value tolerance and a time tolerance, preserving extremes and a minimum set of tangent types. | T1 | 4 |
| MB-ANIM-032 | Resample | Replaces curves by keys at a fixed frame step, either by evaluating or by fitting, for export to formats that store sampled data. | T2 | 4 |
| MB-ANIM-033 | Animation cleanup | Removes redundant keys, keys outside the animation range and static curves, and reports how many keys and curves were removed. | T2 | 4 |
| MB-ANIM-034 | Smoothing and noise filters | Frequency filters smooth or add noise to selected curves with a cutoff or amplitude parameter, with a preview overlay before commit. | T2 | 4 |

## Path animation

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ANIM-035 | Path animation | An object follows a curve through a position parameter that is keyed or driven, with orientation along the path, banking, up vector and an option for constant speed by arc length. | T1 | 4 |
| MB-ANIM-036 | Path speed editing | The speed along a path is edited as a curve of distance against time, with an overlay of velocity, so ease in and out and stops are authored directly. | T2 | 4 |
| MB-ANIM-037 | Flow along multiple paths | An object may blend between several paths with weights, and handoff between paths preserves its world position. | T3 | 4 |

## Camera animation and shot sequencing

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ANIM-038 | Camera animation | Cameras animate position, orientation, focal length, focus distance, aperture and film back; a camera can aim at a target, follow a path, or be controlled by a rig. | T1 | 4 |
| MB-ANIM-039 | Camera shake | A shake layer adds procedural handheld motion with amplitude, frequency and seed per axis, and is non-destructive and bakeable. | T3 | 4 |
| MB-ANIM-040 | Shot sequencing | A sequence arranges shots, each with a camera, a frame range, an offset and handles, in order on a shot track; shots are trimmed, moved, split, grouped and reordered, and the playback range follows the selection. | T1 | 4 |
| MB-ANIM-041 | Shot properties | A shot stores its own camera, notes and optional overrides for visible layers and render settings, and shot data is carried through export and Hub review. | T2 | 4 |
| MB-ANIM-042 | Camera cuts | Cuts between cameras are defined by shots; switching camera at a frame in the viewport and in preview output follows the shot order. | T1 | 4 |
| MB-ANIM-043 | Shot export | Shots export as separate movie or image-sequence outputs, as clips for game export, or as an edit list for a video editor, in one command. | T2 | 4 |

## Playback caching and preview

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ANIM-044 | Playback cache | Evaluated frames, including deformed geometry and simulation states, are stored in memory up to a limit; frames are invalidated by revision when anything upstream changes, so a stale frame is never shown. | T1 | 4 |
| MB-ANIM-045 | Cache fill and scope | The cache fills in the background on worker threads and can be limited to a frame range or to selected content; a timeline strip shows which frames are valid. | T1 | 4 |
| MB-ANIM-046 | Preview render | Captures the viewport through a camera or a sequence to a movie or image sequence with resolution, overlays (frame number, shot name, safe areas), audio and codec options. | T1 | 4 |
| MB-ANIM-047 | Review annotation | A preview can be stamped with a burn-in of scene, shot and frame data and submitted for review through Hub with frame-accurate comments. | T3 | 5 |

## Assisted animation

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ANIM-048 | Assisted in-betweening | Given two or more posed keys on a rig, the assistant proposes in-between frames as new keys on an additive layer, previewed before commit and editable like hand-made keys. | T3 | 5 |
| MB-ANIM-049 | Locomotion generation | For a supported rig archetype (biped, quadruped), generates walk, run and turn motion along a path or toward goals with stride, speed and style parameters, writing keys to the rig's controls on a layer. | T3 | 5 |
| MB-ANIM-050 | Physics-assisted pose polish | An optional pass adjusts a keyed motion toward physically plausible momentum and contact timing while locked keys stay fixed, with a before and after comparison and full undo. | T3 | 5 |
| MB-ANIM-051 | Local model execution | Assisted animation models run on the user's machine or on a user-configured endpoint, and no animation data leaves the machine without an explicit user action. | T2 | 5 |
