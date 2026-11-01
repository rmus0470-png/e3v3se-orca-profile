# Blender: square-to-racetrack cooling duct, restart guide

# Project context (captured 2026-11-01)

Ender-3 V3 SE, dual 5015 blower fan duct project, SE-specific probe clearance, replacement Micro-Swiss FlowTech hotend. Existing KE/SE Fatburner-style ducts were imported as STEP/STL and adjusted in FreeCAD/Blender. Goal: preserve the square fan inlet, transition to a rounded slot/racetrack outlet (two straight sides joined by semicircular ends), lower/reposition the outlet about 6–6.5 mm relative to original, angle it toward the nozzle while avoiding the SE probe finger and hotend, then produce a printable thin-walled duct. User values quick, reproducible exact commands, numeric positioning, editable geometry and verification; avoid restarting the model or speculative UI menu hunts.

Numbers requiring physical validation: 6–6.5 mm nozzle drop is an approximate design target from the discussion, not a final fit measurement; 1.2 or 1.6 mm wall is a starting trial, not a guarantee of printable or heat-safe walls. Existing PETG ducts softened/sagged near the hotend. ASA and reinforcement ribs were considered; check actual temperatures, clearances and firmware limitations before use.

Record of earlier problems: wrong loop selection with Ctrl; bridge twisting; disparate vertex counts between square and racetrack; loft convergence errors; shell/Thickness difficulties; alignment and rotation of imported geometry; probe collision; unsupported bridge areas on print. No confirmed final successful complete duct was reported.

## Starting assets

Prefer a duplicate of the original Blender file with original duct as a reference, plus separate inlet and outlet outlines. Imported FreeCAD STEP generally needs mesh conversion before Blender mesh-editing; STL is mesh already. Determine first whether geometry is an open single surface or an existing two-wall solid. **Do not add Solidify to already thick-walled parts.** Save a version before deleting faces.

## Workflow A: bridge open boundary loops

1. In **Object Mode**, select the two mesh objects and press **Ctrl+J** if they are separate objects. This joins objects, not their vertices.
2. **Tab** into Edit Mode; press **2** (top-row number) for Edge Select. **Alt+click** an edge to select a continuous edge loop. **Shift+Alt+click** a second loop to add it. Topology can prevent this selection from following the desired boundary. Inspect every corner, rotate the view, and use X-ray if necessary.
3. If Ctrl-click selects a shortest path instead of the loop, stop using Ctrl for loop selection. If the existing bridge is malformed, undo or remove only its connecting faces, leaving both boundary loops intact. **X → Only Faces** or **Faces** can differ in what geometry it removes; verify preserved edge rings.
4. Move the outlet loop numerically in Edit Mode: **G, Z, -6.5, Enter**, assuming Blender units are correctly set to millimeters and the model's Z-axis really is the intended nozzle direction. Check the reference object's position; 6.5 is provisional. For an angled movement use constrained axes or Transform panel coordinates rather than assuming world Z.
5. Select both *boundary* loops and invoke **Edge → Bridge Edge Loops** (or search using **F3 → Bridge Edge Loops**). In the **Adjust Last Operation** panel (F9 if needed), vary **Twist** until the surface stops crossing itself. Try **Number of Cuts** 3–4 to insert transition rings; these do not independently define an obstacle-avoiding path. Smoothness/interpolation options vary by Blender release.
6. When bridge creates bad topology, inspect counts and loop ordering. Different vertex counts can be bridged but may create awkward faces. Add vertices by subdividing the square's edges in Edit Mode or rebuild loops with compatible counts. **Ctrl+R Loop Cut** is not generally the correct way to add vertices to a standalone open edge ring.
7. Check duplicate vertices only with care: **A → M → By Distance** merges nearby vertices; use a small threshold so intentional close geometry is not collapsed. Re-check boundary loops after merging. **Shift+N** recalculates normals outside on the selected surface (inspect whether it selects the intended direction).
8. In Object Mode use **Ctrl+A → Scale** (or Apply All Transforms if deliberately needed), then add **Modifier Properties → Add Modifier → Generate → Solidify** (menu organization version dependent). Set trial Thickness **1.2–1.6 mm**, choose Offset direction, enable Even Thickness if appropriate, inspect intersections and the opening. Even Thickness is not a guarantee of uniformly valid geometry around tight turns.
9. Check manifoldness: openings themselves must be surrounded by wall rims; the exported duct must be a watertight solid despite having an air passage. Use Blender's mesh checks/3D Print Toolbox if available, and inspect Cura's sliced preview before printing.

## Workflow B: guide path / curved route

**Important correction to pasted instructions:** a **Curve modifier** deforms *existing mesh geometry*; simply assigning a curve to a square profile does **not** automatically generate a square-to-slot transition or an extrusion that reaches the outlet. Likewise, LoopTools availability and command names depend on Blender version/extensions; do not assume a built-in LoopTools “Loft” that follows any arbitrary guide path.

For obstacle avoidance, the dependable mesh workflow is to create intermediate rings along the route, move/rotate them to clear the SE probe, and bridge adjacent rings or extrude and adjust rings manually. Keep the two ends fixed and progressively shape the center section. If using a Curve modifier, first create a sufficiently subdivided tube in a straight orientation, match the modifier's deformation axis to the curve's local axis, then deform; fitting a different outlet shape still requires separate modeling.

## Troubleshooting checklist

- **Bridge twists:** Confirm two entire open loops are selected, not a shortest path; adjust Twist; inspect vertex ordering; remove duplicate or stray vertices.
- **Bridge does nothing:** Confirm both loops are part of the same mesh object, both are open boundaries, and only the intended edges are selected.
- **Solidify produces ugly bulges:** Apply Scale, inspect normals and nonmanifold edges, reduce thickness or smooth a tight turn, check self-intersections. Never assume applying scale guarantees success.
- **Imported model becomes hard to edit:** Keep STEP/parametric master in FreeCAD; use Blender for mesh edits only, and save named checkpoints.
- **Probe collision:** Use the real toolhead model or measurements as reference; check fan inlet, probe finger, nozzle and bed travel at all Z positions.

## Resume point / unfinished decisions

Open the latest saved blend and establish: (a) open loops or pre-thickened solid? (b) which edges are selected? (c) actual transformed outlet location and fan/SE probe clearance? (d) wall thickness feasible without intersections? Final geometry and print are not yet confirmed. Keep changes incremental, test with a short partial prototype before a full duct print.
