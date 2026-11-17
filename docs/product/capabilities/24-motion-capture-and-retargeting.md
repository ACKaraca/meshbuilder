# Motion capture and retargeting

This area specifies how MeshBuilder ingests captured performances, solves them onto characters, retargets motion between characters of different proportions, cleans and filters data, streams live from capture devices and virtual cameras, and records takes. A full-body solver with effectors, pinning and floor contacts runs in real time, and the results are ordinary animation that the rest of the animation tools edit.

## Domain objects

| Object | Key properties | Relationships |
|---|---|---|
| Capture Take | Source file or stream, frame rate, marker or joint channels, units, up axis, notes | Imported into a scene Take; feeds Actors or Skeleton channels |
| Marker Set | Marker names, segment association, offsets, preset type | Defines an Actor; matched to the marker names in a Capture Take |
| Marker Trajectory | Name, per-frame position, validity flag per frame, gap list | Belongs to a Capture Take; edited by gap fill and filters |
| Actor | Marker Set, fitted dimensions, solved actor skeleton, active flag | Solves a performance from markers; feeds Character Definitions |
| Character Definition | Skeleton slot mapping (standard slots plus extras), stance pose, solver settings | Maps one Skeleton to the solver's standard body; target and source of retargeting |
| Effector | Target joint or point, translation and rotation goal, pull, reach, pin state | Part of a solver rig; manipulated by users or driven by data |
| Floor Contact | Contact points per foot and hand, height, depth, sensitivity, plane | Part of a Character Definition; adjusts solved pose to stay above a surface |
| Retarget Mapping | Source Definition, target Definition, scale rules, root handling, per-limb options | Produces target animation from source motion |
| Cleanup Filter | Type, parameters, channel scope, range | Applied to Marker Trajectories or animation curves |
| Capture Device | Device type, connection settings, channel mapping, online and recording flags | Streams data into scene attributes or Actors |
| Virtual Camera Source | Tracked transform, lens data, smoothing | Drives a scene Camera |
| Video Capture Job | Input video, subject count, output skeleton, confidence data | Produces a Capture Take through a markerless estimator |
| Batch Job | File list, operation sequence, output rules, log | Runs import, solve, retarget, filter and export without a UI |

## Import and formats

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOCAP-001 | Marker data import | Reads optical marker data from C3D and tabular marker files into a Capture Take with units, frame rate, marker names and per-frame validity, and reports unsupported or malformed content without failing the import. | T1 | 4 |
| MB-MOCAP-002 | Skeletal capture import | Reads skeletal motion from BVH, FBX and OpenUSD skeletal animation with joint hierarchy, rotation order, units and frame rate, and shows the mapping to the target before applying. | T1 | 4 |
| MB-MOCAP-003 | Import options and scale | Import choices cover unit conversion, up axis, frame range, rotation order and root handling, and the document records the source file and options for re-import. | T1 | 4 |
| MB-MOCAP-004 | Capture library browsing | Imported takes appear in a library panel with thumbnails and metadata, and are dragged onto a character or into the clip arrangement. | T2 | 4 |
| MB-MOCAP-005 | Export of motion | Cleaned motion exports as BVH, FBX, glTF and OpenUSD skeletal animation with frame range, sampling rate, bake options and root treatment. | T1 | 4 |
| MB-MOCAP-006 | Facial capture data import | Reads facial channel data (blend shape weight streams and tracked point sets) from common file layouts and maps channels to a character's blend shape set. | T2 | 4 |

## Marker cleanup

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOCAP-007 | Gap detection | Lists every marker gap by duration and neighbors, flags gaps that are too long to fill reliably, and shows gaps on a timeline strip per marker. | T1 | 4 |
| MB-MOCAP-008 | Gap fill | Fills gaps by interpolation along the marker's own path, by reference to rigid-body neighbors on the same segment, or by pattern from other markers, with a method chosen per gap and a preview. | T1 | 4 |
| MB-MOCAP-009 | Marker swap repair | Detects frames where two markers exchange identity and offers to swap them back across a range, using continuity of position and velocity. | T1 | 4 |
| MB-MOCAP-010 | Marker naming and labeling | Unlabeled trajectories are matched to a Marker Set by position at a reference frame, with manual assignment and a report of unmatched and extra markers. | T1 | 4 |
| MB-MOCAP-011 | Marker spike and noise filtering | Removes spikes and smooths noise on marker trajectories with filters whose cutoffs and thresholds are shown on the curve before applying. | T1 | 4 |

## Actor fitting and marker solving

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOCAP-012 | Marker set definition | A Marker Set assigns each marker to a body segment with an offset, and presets cover common full-body, upper-body and hand layouts; custom sets are saved to libraries. | T1 | 4 |
| MB-MOCAP-013 | Actor fitting | Fits an Actor to a calibration pose frame by solving segment lengths and marker offsets, then reports the residual error per marker. | T1 | 4 |
| MB-MOCAP-014 | Marker solving | Solves per-frame actor pose from marker positions with segment rigidity, joint limits and temporal smoothing, producing joint transforms for the actor skeleton. | T1 | 4 |
| MB-MOCAP-015 | Solve diagnostics | Per-frame and per-marker residuals are drawn as a heat strip and as marker coloring in the viewport, with a list of the worst frames to open. | T2 | 4 |
| MB-MOCAP-016 | Rigid body markers | Marker clusters on props and cameras solve as rigid bodies with a stable pivot and are exported as ordinary transforms. | T2 | 4 |
| MB-MOCAP-017 | Hand and finger solving | Hand marker sets or glove data solve finger joint rotations with joint limits and coupling options. | T2 | 4 |

## Character definition and full-body solver

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOCAP-018 | Character definition | A Skeleton is mapped to the standard body slots (hips, spine, neck, head, arms, legs, hands, fingers, toes and extras) by auto-detection from labels with manual override; validation reports missing or ambiguous slots before the definition is locked. | T1 | 4 |
| MB-MOCAP-019 | Stance pose | A definition stores a stance pose, usually a T-pose or A-pose, used for solving and retargeting, and a command conforms the skeleton's pose to the stance. | T1 | 4 |
| MB-MOCAP-020 | Extra chains | Tails, props, ponytails and extra spine or neck joints attach to a definition as extension chains that follow a parent slot without being solved by the body solver. | T2 | 4 |
| MB-MOCAP-021 | Full-body solver | Solves a character's pose from end-effector goals with a biomechanical model: moving a hand or foot effector distributes motion through the body, with per-body-part stiffness, reach and pull; solves in real time on typical characters. | T1 | 4 |
| MB-MOCAP-022 | Effector manipulation modes | Moving an effector can affect the full body, a body part, or the selection alone, and keying follows the chosen mode. | T1 | 4 |
| MB-MOCAP-023 | Solver control rig | A character definition can generate a control rig of effectors with FK and IK blending per limb that animators edit on top of captured or generated motion. | T1 | 4 |
| MB-MOCAP-024 | Solver settings | Per-definition settings cover hip level, feet spacing, mirror, spine and neck compensation, roll solving and body-part stiffness; presets store common values. | T2 | 4 |
| MB-MOCAP-025 | Quadruped definition | A definition type for four-legged characters with leg, spine, neck and tail slots solved by the same effector model. | T3 | 5 |

## Pinning and floor contact

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOCAP-026 | Effector pinning | An effector can be pinned in translation, rotation or both, holding its goal in world space while others move; feet are pinned by default and "release all" frees them temporarily. | T1 | 4 |
| MB-MOCAP-027 | Reach and pull | Each effector has reach values for translation and rotation that set how strongly it achieves its goal, and a pull value that sets how much it drags connected parts. | T1 | 4 |
| MB-MOCAP-028 | Floor contact | Feet and hands have contact points that stay above or on a floor plane with height, depth and sensitivity settings; the solver adjusts the pose and hips so contacts do not penetrate. | T1 | 4 |
| MB-MOCAP-029 | Foot contact detection | Contact frames are detected from foot velocity and height and are editable on a timeline, with automatic pinning of the foot during contact. | T1 | 4 |
| MB-MOCAP-030 | Foot slide cleanup | A command removes foot sliding over detected contacts by pinning the foot and re-solving the leg and hips, with a report of the corrected distance. | T1 | 4 |
| MB-MOCAP-031 | Ground and surface contact | Contacts may use any collision surface rather than a plane, so a character steps on stairs or terrain, solved per frame. | T3 | 5 |

## Retargeting

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOCAP-032 | Retargeting between characters | A target Definition takes a source Definition or Actor as input and reproduces its motion through the solver, adapting to different limb lengths and proportions while preserving contacts. | T1 | 4 |
| MB-MOCAP-033 | Retarget root and scale | Root motion is scaled by leg length ratio or by a chosen factor, with options for in-place output, ground-relative height and heading-only root. | T1 | 4 |
| MB-MOCAP-034 | Retarget tuning | Per-limb options adjust reach, offsets, stance difference and hand position preservation, and a comparison view overlays source and target. | T2 | 4 |
| MB-MOCAP-035 | Bake retargeted motion | A retarget result bakes to keys on the target skeleton or its control rig with filters for unrolling rotations and reducing keys. | T1 | 4 |
| MB-MOCAP-036 | Retarget without a rig | Retargeting works between skeletons with only a character definition and no control rig, and between a skeleton and a rig built from a template. | T2 | 4 |
| MB-MOCAP-037 | Face retargeting | Facial capture channels retarget to another character's blend shape set through a channel mapping with ranges and offsets, and the mapping is saved with the character. | T2 | 4 |
| MB-MOCAP-038 | Prop interaction retarget | Contacts between hands and props are preserved while retargeting by constraining the prop to a hand on contact frames. | T3 | 5 |

## Cleanup filters

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOCAP-039 | Frequency filter | A low-pass filter smooths animation curves with a cutoff in hertz or frames, previewed on the curve and applied to a channel scope. | T1 | 4 |
| MB-MOCAP-040 | Peak removal | Detects and removes single-frame outliers from curves with a tolerance and replaces them by interpolation. | T1 | 4 |
| MB-MOCAP-041 | Key reduction | Replaces dense captured keys by a sparse set within a tolerance, per channel or per body part, preserving contacts and extremes. | T1 | 4 |
| MB-MOCAP-042 | Rotation unroll | Removes flips and unwraps rotations on captured channels, with a quaternion-based option, so curves are editable. | T1 | 4 |
| MB-MOCAP-043 | Time shift and resample | Shifts captured data in time and resamples it to the scene frame rate with interpolation that preserves contact timing. | T2 | 4 |
| MB-MOCAP-044 | Root and origin alignment | Aligns captured motion to a world origin and heading at a chosen frame, and removes drift in height with a ground-plane fit. | T1 | 4 |

## Live streaming and recording

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOCAP-045 | Device connection | A device manager connects to optical, inertial and face-tracking sources over network protocols and plug-ins, shows connection state and channel lists, and maps channels to Actors, Skeletons or attributes. | T1 | 4 |
| MB-MOCAP-046 | Live characters | A character driven by a device shows the performance live through the solver, with separate online and recording switches and a latency display. | T1 | 4 |
| MB-MOCAP-047 | Recording | Device streams record into a take at the scene frame rate, together with timecode and audio when present, and recording is crash-safe so a session is recoverable after a failure. | T1 | 4 |
| MB-MOCAP-048 | Stream output to other applications | The current scene's animated skeleton, camera and properties stream to other applications over a documented local protocol, including a game engine and RenderBuilder. | T2 | 5 |
| MB-MOCAP-049 | Virtual camera input | A tracked handheld device or a phone-class sensor drives a scene camera with smoothing, scale, axis locks and lens controls, and the motion records to keys. | T2 | 4 |
| MB-MOCAP-050 | Real-time playback of captured data | Captured takes and live streams play at interactive rates with the solver and deformers active, and the display shows frame rate and solver time. | T1 | 4 |
| MB-MOCAP-051 | Timecode and sync | Takes carry timecode and sync to audio and to external time sources, with offset correction applied when merging takes from several devices. | T2 | 4 |

## Markerless capture and batch processing

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOCAP-052 | Markerless capture import | Reads skeletal animation and confidence data produced from video by an external estimator (BVH, FBX or OpenUSD) and shows per-joint confidence on the timeline for targeted cleanup. | T2 | 4 |
| MB-MOCAP-053 | Video-based capture job | A configurable job runs a locally hosted or user-chosen estimator on a video file to produce a Capture Take; no video leaves the machine unless the user selects a remote endpoint. | T3 | 5 |
| MB-MOCAP-054 | Batch processing | A batch job applies a sequence of operations (import, gap fill, filter, solve, retarget, reduce, export) to a file list headlessly, writes a log of every file and failure, and continues after errors. | T1 | 4 |
| MB-MOCAP-055 | Cleanup presets | A saved sequence of filters and parameters is applied to a selection of takes as one command and one undo step, and can be shared in a project library. | T2 | 4 |
