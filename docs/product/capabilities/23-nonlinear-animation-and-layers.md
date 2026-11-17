# Nonlinear animation and layers

This area specifies how MeshBuilder arranges, blends and reuses animation without editing keys directly: clips on tracks, cross-fades, time warping, looping, additive and override layers, merging and baking, animation character sets that let clips move between rigs, and takes. All of it is stored as document data, so a clip arrangement is versioned, comparable and mergeable like any other content.

## Domain objects

| Object | Key properties | Relationships |
|---|---|---|
| Clip Source | Name, frame range, channel list, source kind (keys, cache, imported file, generated) | Referenced by Clips; may be shared, linked to a file or embedded |
| Clip | Source reference, start time, in and out trim, speed, scale, loop count, offset mode, weight curve, enabled flag | Placed on a Track; drives the channels of a Character Set |
| Track | Kind (character, camera, audio, attribute), order, mute, solo, lock, blend mode | Contains Clips; tracks of one kind blend top to bottom |
| Blend | Overlap range, curve, mode (cross-fade, additive, transition match) | Between two Clips on a Track or across tracks |
| Time Warp Curve | Curve of source time against output time, extrapolation | Attached to a Clip or Track to retime playback |
| Animation Layer | Name, mode (override or additive), weight, mute, solo, lock, rotation accumulation, member channels | Holds layer keys for a set of attributes; stacked above base animation |
| Layer Weight Curve | Curve of weight over time | Controls a Layer's or Clip's contribution |
| Character Set | Name, channel list with stable control IDs, rig template, mapping | Defines which attributes a Clip may drive; links a Clip to one or more rigs |
| Clip Library | Clip Sources with thumbnails, categories, rig template | Shared across documents and Hub projects |
| Take | Name, frame range, notes, flags, owned animation layers | Alternative animation state of the same scene objects |

## Clips and tracks

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-NLA-001 | Clip creation from animation | Creates a Clip Source from the keys of selected objects over a range, from an animation layer, or from an imported animation file, and places a Clip on a track. | T1 | 4 |
| MB-NLA-002 | Clip creation from caches and audio | Geometry caches, simulation caches and audio files become clips that are retimed, trimmed and blended like keyed clips. | T2 | 4 |
| MB-NLA-003 | Tracks | A scene holds tracks of kinds character, camera, audio and attribute; tracks reorder, mute, solo, lock and group in folders, and a track outputs only the channels its clips drive. | T1 | 4 |
| MB-NLA-004 | Clip editing | Clips are moved, trimmed at start or end, split, duplicated, deleted and snapped to other clips, markers or frames; ripple options open or close gaps. | T1 | 4 |
| MB-NLA-005 | Clip groups | Several clips are grouped to move, scale and mute together; a group is nestable and can be turned into a single clip source. | T2 | 4 |
| MB-NLA-006 | Clip source linking | A clip refers to its source by link or by embedding; linked sources update clips when the source changes, and "make local" copies the source into the document. | T1 | 4 |
| MB-NLA-007 | Source swap | A clip's source, target character or media file can be replaced without rebuilding the arrangement, with a channel mapping report. | T2 | 4 |
| MB-NLA-008 | Clip display | Clips show name, frame range, speed, loop markers and thumbnails of the first and last pose; the track view zooms and frames by selection. | T1 | 4 |

## Blending and cross-fades

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-NLA-009 | Cross-fade between clips | Overlapping clips on one track blend across the overlap with a curve (linear, ease, custom), and the overlap length is adjustable by dragging. | T1 | 4 |
| MB-NLA-010 | Transition matching | A transition can align the next clip's root position and heading to the previous clip's at a chosen frame, optionally carrying the offset through the clip, to avoid pops. | T1 | 4 |
| MB-NLA-011 | Vertical track blending | Clips on stacked tracks blend by track order using each clip's weight curve, in override or additive mode per track. | T1 | 4 |
| MB-NLA-012 | Rotation blending | Rotations blend by quaternion interpolation on shortest path regardless of the clip's stored rotate order, so cross-fades through large rotations do not flip. | T1 | 4 |
| MB-NLA-013 | Per-channel masks | A clip or track can apply to a subset of channels (upper body, hands, face) chosen from named sets, so clips combine across body parts. | T1 | 4 |
| MB-NLA-014 | Weight curves | A clip's weight is animated by a curve over its duration, with fade-in and fade-out presets. | T1 | 4 |

## Time warp and speed curves

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-NLA-015 | Clip speed and scale | A clip plays at a constant speed or is scaled to a new duration; a reverse option plays the clip backward. | T1 | 4 |
| MB-NLA-016 | Time warp curve | A curve maps output time to source time on a clip or track, edited as a function curve with tangent controls, so ease in and out, holds and speed changes are authored without moving keys. | T1 | 4 |
| MB-NLA-017 | Warp with markers | Source markers can be pinned to output times so events (footfalls, impacts) line up while the motion between them is retimed. | T2 | 4 |
| MB-NLA-018 | Warp bake | A time warp is baked into the clip source as new keys, with an option to keep both. | T2 | 4 |

## Looping and offsets

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-NLA-019 | Clip looping | A clip repeats a set number of times or fills a range; each repeat can accumulate the root offset so a looped walk advances through space. | T1 | 4 |
| MB-NLA-020 | Loop cleanup | A command blends the end of a clip toward its start over a chosen number of frames to make an exact loop, and reports the remaining mismatch. | T2 | 4 |
| MB-NLA-021 | Offset modes | A clip's value is applied as absolute, relative to the value at clip start, or relative to the previous clip's end, chosen per channel group. | T1 | 4 |
| MB-NLA-022 | Cycle offset by attribute | A clip placed on a character can offset its start by an attribute or random seed, so many characters playing the same clip do not move in unison. | T2 | 4 |

## Additive and override layers

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-NLA-023 | Animation layers | A layer holds keys for chosen attributes above the base animation; a layer is either additive, adding its values to those below, or override, replacing them by weight. | T1 | 4 |
| MB-NLA-024 | Layer weights | Layer weight is a value from 0 to 1 that can be keyed or driven, and a weight curve may exceed the range in a labeled extended mode. | T1 | 4 |
| MB-NLA-025 | Layer control | Layers mute, solo, lock, reorder, rename, color and collapse, and the layer receiving new keys is shown prominently; keys go only to the selected layer. | T1 | 4 |
| MB-NLA-026 | Layer membership | Adding objects or attributes to a layer copies their current values as the layer's base; removing them restores the value below. | T1 | 4 |
| MB-NLA-027 | Rotation accumulation | An additive layer applies rotation as an Euler sum or as a quaternion product, selectable per layer, with the effect described in the layer's tooltip. | T2 | 4 |
| MB-NLA-028 | Layers across clips | A layer can be applied to a clip or character set rather than to a single timeline so an adjustment stays with the clip when it moves. | T2 | 4 |

## Layer merge and baking

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-NLA-029 | Merge layers | Merging flattens selected layers into the layer below or into base animation by evaluating the stack, with keys written at original key times or at every frame and optional simplification. | T1 | 4 |
| MB-NLA-030 | Bake clips to keys | The result of a track arrangement over a range is baked into keys on the scene objects, optionally into a new take, with an option to keep the clip arrangement muted. | T1 | 4 |
| MB-NLA-031 | Bake with solvers and constraints | Baking reads the fully evaluated rig, including solvers and constraints, so the baked keys reproduce the visible result on the controls or on the skeleton, as chosen. | T1 | 4 |
| MB-NLA-032 | Layer export | Layers and clips can be exported as separate animation files for exchange, keeping layer names and weights where the format allows them. | T2 | 4 |

## Character sets and sharing

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-NLA-033 | Character set | A character set lists the attributes a rig exposes to clips, by stable control ID, and is generated from a rig assembly or built by hand. | T1 | 4 |
| MB-NLA-034 | Clip sharing between characters | A clip authored on one rig plays on another rig built from the same template through control IDs, and a mapping editor handles rigs with a different but compatible control set. | T1 | 4 |
| MB-NLA-035 | Clip sharing with retargeting | A clip authored on one skeleton plays on another of different proportions through the retargeting solver (see MB-MOCAP), with the retarget stored on the clip. | T2 | 4 |
| MB-NLA-036 | Clip library | Clip sources are stored in libraries with thumbnails, categories and rig template, browsed in a panel and dragged onto tracks; libraries live in files or on Hub. | T1 | 4 |
| MB-NLA-037 | Clip preview | A clip is previewed on a rig in the viewport from the library without placing it on a track. | T2 | 4 |
| MB-NLA-038 | Game export of clips | Selected clips export as separate animation clips with preset frame rate, root handling and naming, in glTF, FBX and OpenUSD skeletal formats. | T1 | 4 |

## Take management

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-NLA-039 | Takes | A take stores an alternative animation state of the same scene, including its layers and clip arrangement; creating, duplicating, renaming and deleting takes are commands, and switching a take swaps animation data but not scene objects. | T1 | 4 |
| MB-NLA-040 | Take notes and flags | A take has notes, a status flag (draft, selected, approved) and a frame range, so a session's alternatives are reviewed and chosen. | T2 | 4 |
| MB-NLA-041 | Take comparison | Two takes display side by side or overlaid with ghosting, and a comparison lists the channels that differ. | T3 | 4 |
| MB-NLA-042 | Take recording | Live input and capture streams record into the current take at the scene frame rate, and a recording never overwrites a take without a confirmation or a new take. | T1 | 4 |
| MB-NLA-043 | Take merge | Selected channels and ranges from one take merge into another with an overwrite or blend mode, and the operation is one undoable transaction. | T2 | 4 |
