# Reference workflows

End-to-end workflows that serve as acceptance scenarios for milestones in the
[roadmap](../roadmap.md). Each ends with observable acceptance results tied to capability
identifiers.

## W01. Hard-surface prop to game-ready export

1. Set the document unit to centimetres and the frame rate to 30 (MB-PROJ-008, MB-PROJ-010).
2. Load a front and a side reference image on image planes (MB-VIEW-007).
3. Block the form with a box and a cylinder primitive; keep primitive parameters live (MB-POLY-001).
4. Add detail with extrude, inset, insert edge loop and multi-cut (MB-POLY-021, MB-POLY-022,
   MB-POLY-012, MB-POLY-034).
5. Cut vents with a boolean modifier using a collection of cutters; add a shape-following bevel
   after the boolean (MB-MOD-025, MB-TOPO-003, MB-POLY-033).
6. Add a mirror modifier and an array modifier for repeated bolts (MB-MOD-017, MB-MOD-014).
7. Set weighted normals (MB-ATTR-009) and preview subdivision with creases for the high version
   (MB-SUBD-004, MB-SUBD-008).
8. Build the low version by applying a copy of the stack and decimating (MB-MOD-003, MB-TOPO-014).
9. Mark seams automatically, unfold, pack at 2048 pixels with 8 pixels padding and set texel
   density (MB-UV-009, MB-UV-011, MB-UV-017, MB-UV-022).
10. Run the "game export" check preset and export with the game-ready glTF preset
    (MB-TOPO-027, MB-IO-024, MB-IO-009).

**Acceptance**

- Changing a cutter's position after step 8 updates the high version and the bevel without manual
  repair (MB-MOD-025, MB-POLY-033).
- The mesh check reports zero non-manifold edges and zero flipped faces on the low version
  (MB-TOPO-023).
- The exported GLB opens in an independent glTF validator with no errors, in metres, Y-up
  (MB-IO-001, MB-IO-006).
- The loss report lists every approximation made on export (MB-IO-002).

## W02. Character base mesh, retopology and UVs

1. Import a dense scan or sculpt as OBJ (MB-IO-011).
2. Create a new mesh and retopologize with draw-on-surface, strip and contour tools, using
   topological symmetry (MB-TOPO-017, MB-TOPO-018, MB-TOPO-021, MB-VIEW-031).
3. Relax the new mesh on the reference (MB-TOPO-019).
4. Check subdivision topology and fix poles flagged by the check (MB-SUBD-016).
5. Mark seams, unfold, straighten the strip shells and pack into four UDIM tiles by body part
   (MB-UV-008, MB-UV-011, MB-UV-013, MB-UV-021, MB-UV-023).
6. Hand the mesh to sculpting and baking ([25-sculpting.md](capabilities/25-sculpting.md),
   [26-texture-painting-and-baking.md](capabilities/26-texture-painting-and-baking.md)).

**Acceptance**

- The retopologized mesh lies within the configured distance of the reference at every vertex
  (MB-TOPO-022).
- UV distortion display shows no face above the configured area distortion threshold (MB-UV-004).
- Shells occupy only their assigned tiles with consistent texel density (MB-UV-022, MB-UV-024).

## W03. Procedural environment kit

1. Create a graph asset that generates a modular wall panel from width, height and bevel inputs,
   with published parameters (MB-GRAPH-006, MB-GRAPH-007, MB-GRAPH-011, MB-GRAPH-012).
2. Use the asset as a graph modifier on several objects with different parameter values
   (MB-MOD-039).
3. Scatter rocks and plants over a terrain with density from a painted attribute and minimum spacing
   (MB-MOD-015, MB-ATTR-014).
4. Use a repeat zone to stack panels and a for-each zone to vary each instance (MB-GRAPH-024,
   MB-GRAPH-025).
5. Profile the graph and fix the most expensive node (MB-GRAPH-009, MB-SCENE-039).
6. Bake the scattered result to instances for export (MB-GRAPH-030).

**Acceptance**

- Editing the graph asset updates every object that uses it, unless the object pins a version
  (MB-GRAPH-006).
- The same document and seed produce bit-identical scatter results on two machines with different
  core counts (MB-SCENE-040).
- The viewport keeps navigation interactive while scattered instances are recomputed in the
  background (MB-SCENE-037).

## W04. Product visualization from a SolidBuilder model

1. Link an Arc Scene published by SolidBuilder (MB-ASSET-011).
2. Assign OpenPBR materials from a library to linked parts by stable identifiers (MB-ASSET-012,
   MB-LOOK-024).
3. Light with a studio HDR environment and two rectangle lights in physical units (MB-LIGHT-012,
   MB-LIGHT-001, MB-LIGHT-002).
4. Set up a physical camera with depth of field focused on a picked surface (MB-LIGHT-016,
   MB-LIGHT-018).
5. Store three material variants for color options (MB-LOOK-020).
6. Render each variant through RenderBuilder with beauty and matte identifier passes
   (MB-RENDER-010, MB-RENDER-014).
7. Receive an updated SolidBuilder version and review the change report (MB-ASSET-013).

**Acceptance**

- Material assignments persist on all parts that still exist after the source update
  (MB-ASSET-012).
- The change report lists added, removed and modified parts (MB-ASSET-013).
- Each variant renders to a separately named file set (MB-RENDER-017).

## W05. Architectural visualization from an ArchBuilder model

1. Link an Arc Scene published by ArchBuilder with its geographic reference (MB-ASSET-011,
   MB-PROJ-012).
2. Replace materials by material name across the model (MB-LOOK-019).
3. Add a physical sky from location, date and time and animate the sun over a day (MB-LIGHT-013).
4. Place portals at window openings (MB-LIGHT-007).
5. Populate furniture from the asset browser as instances and place vegetation on the site with
   surface scatter (MB-ASSET-015, MB-MOD-015).
6. Create render setups for day and dusk with overrides (MB-RENDER-015).
7. Render through RenderBuilder with two-point perspective correction (MB-LIGHT-021).

**Acceptance**

- Geometry far from the origin displays without jitter (MB-VIEW-011).
- Switching render setups changes only overridden values; the document is unchanged after
  switching back (MB-RENDER-015, MB-RENDER-016).
- The interactive render view updates within one renderer frame interval of a camera move
  (MB-RENDER-009).

## W06. USD shot assembly with variants and layer overrides

1. Create a shot stage from the department template (MB-USD-023).
2. Reference an environment and two character assets as payloads; open with payloads unloaded and
   load only the characters (MB-USD-014, MB-USD-002).
3. Set the edit target to the layout layer and move props (MB-USD-009).
4. Switch a prop's variant with the variant manager (MB-USD-016).
5. Convert one character prim to native objects, adjust the pose, and write it back as an override
   (MB-USD-011).
6. Run asset path diagnostics and stage validation; repath a moved texture folder (MB-USD-021,
   MB-USD-022).
7. Save the edited layers (MB-USD-010).

**Acceptance**

- Edits land only in the selected edit-target layer; other layers are byte-identical
  (MB-USD-009, MB-USD-010).
- The saved stage opens in an independent USD reader with the same prim hierarchy and variant
  selections.
- Diagnostics report zero unresolved paths after repathing (MB-USD-021).

## W07. Look development and turntable

1. Open the look-development workspace (MB-LOOK-023).
2. Build a layered material graph with a MaterialX texture network and UDIM textures in the correct
   input color spaces (MB-LOOK-002, MB-LOOK-005, MB-LOOK-010, MB-LOOK-011).
3. Compare two materials side by side under the same environment (MB-LOOK-023).
4. Capture a viewport turntable and render a turntable through RenderBuilder (MB-RENDER-006,
   MB-RENDER-010).
5. Export the material as a MaterialX document and add it to a library (MB-LOOK-025, MB-LOOK-024).

**Acceptance**

- The viewport and RenderBuilder interpret every OpenPBR parameter the same way within the
  documented viewport approximations listed in the conversion report (MB-LOOK-001, MB-LOOK-026).
- The exported MaterialX document validates against the MaterialX specification.
- Render output is written in the chosen output color space (MB-PROJ-006).

## W08. Character rig and walk cycle

1. Build a skeleton, solvers and controls ([20-rigging.md](capabilities/20-rigging.md)).
2. Bind the mesh and refine weights with the attribute-paint brush
   ([21-skinning-and-deformation.md](capabilities/21-skinning-and-deformation.md), MB-ATTR-014).
3. Key a walk cycle and refine curves ([22-keyframe-animation.md](capabilities/22-keyframe-animation.md)).
4. Play back from the frame cache and profile the rig (MB-SCENE-038, MB-SCENE-039).
5. Capture a viewport preview (MB-RENDER-005) and export the cycle as a glTF animation clip
   (MB-IO-009).

**Acceptance**

- Playback from a filled cache runs at the scene frame rate (MB-SCENE-038).
- Editing a key invalidates only the affected frames of the affected objects (MB-SCENE-038).
- The exported clip plays in an independent glTF viewer with matching joint transforms.

## W09. Motion-capture clean-up and retarget

1. Import marker data and fit an actor
   ([24-motion-capture-and-retargeting.md](capabilities/24-motion-capture-and-retargeting.md)).
2. Retarget to the W08 character and polish on an additive layer
   ([23-nonlinear-animation-and-layers.md](capabilities/23-nonlinear-animation-and-layers.md)).
3. Export the result as FBX and USD (MB-IO-010, MB-IO-012).

**Acceptance**

- The FBX export runs through the isolated conversion process; disabling that process leaves the
  USD export available (MB-IO-010).
- The loss report lists any constraint or layer data flattened on export (MB-IO-002).

## W10. Effects shot with simulation and caches

1. Build a destruction setup from fractured pieces (MB-TOPO-010) and simulate
   ([27-rigid-cloth-and-hair-dynamics.md](capabilities/27-rigid-cloth-and-hair-dynamics.md)).
2. Add smoke ([28-particles-fluids-and-volumes.md](capabilities/28-particles-fluids-and-volumes.md))
   and display the volume (MB-SCENE-007).
3. Export geometry as Alembic and the volume as OpenVDB (MB-IO-018, MB-IO-020).
4. Reference the caches in a lighting document through streaming (MB-IO-023) and render
   (MB-RENDER-010).

**Acceptance**

- Cached geometry streams per frame within the memory budget (MB-IO-023).
- Re-running the simulation with the same seed produces identical cache files (MB-SCENE-040).

## W11. Motion graphics title sequence

1. Create text curves and extrude them with bevels (MB-CURVE-004, MB-CURVE-023).
2. Distribute instances with a procedural network and falloffs
   ([29-motion-graphics-and-crowds.md](capabilities/29-motion-graphics-and-crowds.md)), built on
   point and instance nodes (MB-GRAPH-016).
3. Render the sequence through RenderBuilder with output naming tokens (MB-RENDER-010,
   MB-RENDER-017).

**Acceptance**

- Instances render as instances in the Arc Scene, not as merged geometry (MB-RENDER-007).
- Changing the text string updates the whole effect without manual steps (MB-GRAPH-002).

## W12. Scan clean-up and level-of-detail chain

1. Import a point cloud and a photogrammetry mesh (MB-IO-021, MB-IO-011).
2. Run the mesh check and automatic fixes (MB-TOPO-023, MB-TOPO-024).
3. Voxel-remesh, then generate an automatic quad retopology at 20,000 faces (MB-TOPO-011,
   MB-TOPO-013).
4. Transfer vertex colors from the scan onto the retopologized mesh (MB-ATTR-018).
5. Generate a three-level LOD chain and export it (MB-TOPO-016, MB-IO-024).

**Acceptance**

- Automatic retopology output is within 10 percent of the target face count and quad-dominant
  (MB-TOPO-013).
- The LOD chain exports as LOD groups in formats that support them (MB-TOPO-016).

## W13. Headless conversion and validation in CI

1. Run `meshbuilder-cli` on a folder of OBJ, FBX and glTF files with a conversion preset to USD
   (MB-SCRIPT-012, MB-IO-008).
2. Run a mesh check preset and stage validation on each output (MB-TOPO-027, MB-USD-022).
3. Evaluate a graph asset with parameters from a JSON file and write the result (MB-GRAPH-032).
4. Capture a viewport thumbnail per asset (MB-RENDER-005).
5. Run the same job as an Arc Hub automation job (MB-SCRIPT-013).

**Acceptance**

- The CLI exits non-zero when any file fails validation and writes a JSON report per file
  (MB-SCRIPT-012, MB-IO-002).
- No dialog or window opens during the run (MB-SCRIPT-005).
- A malformed input file is rejected with a message and does not stop the batch (MB-IO-007).

## W14. Studio tool and configuration roll-out

1. Write a Python tool that renames objects by project naming rules and registers a panel and a menu
   entry (MB-SCRIPT-006, MB-SCRIPT-008, MB-PROJ-017).
2. Record a macro for a repetitive clean-up and bind it to a radial menu (MB-SCRIPT-004,
   MB-CUSTOM-007).
3. Package the tool, macro, a WebAssembly modifier plug-in, a workspace and a key map (MB-SCRIPT-016,
   MB-CUSTOM-003).
4. Install the package on another machine with a studio configuration folder (MB-CUSTOM-016).

**Acceptance**

- The package manager shows the plug-in's requested capabilities before install, and the plug-in
  cannot read files outside a granted folder (MB-SCRIPT-015).
- The tool works identically against the desktop application and a headless session
  (MB-SCRIPT-006).
- Opening a document that references the tool runs no code until the user acts (MB-SCRIPT-010).
