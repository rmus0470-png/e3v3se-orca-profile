# FreeCAD: parametric square-to-racetrack duct, restart guide

# Project context (captured 2026-11-01)

Ender-3 V3 SE, dual 5015 blower fan duct project, SE-specific probe clearance, replacement Micro-Swiss FlowTech hotend. Existing KE/SE Fatburner-style ducts were imported as STEP/STL and adjusted in FreeCAD/Blender. Goal: preserve the square fan inlet, transition to a rounded slot/racetrack outlet (two straight sides joined by semicircular ends), lower/reposition the outlet about 6–6.5 mm relative to original, angle it toward the nozzle while avoiding the SE probe finger and hotend, then produce a printable thin-walled duct. User values quick, reproducible exact commands, numeric positioning, editable geometry and verification; avoid restarting the model or speculative UI menu hunts.

Numbers requiring physical validation: 6–6.5 mm nozzle drop is an approximate design target from the discussion, not a final fit measurement; 1.2 or 1.6 mm wall is a starting trial, not a guarantee of printable or heat-safe walls. Existing PETG ducts softened/sagged near the hotend. ASA and reinforcement ribs were considered; check actual temperatures, clearances and firmware limitations before use.

Record of earlier problems: wrong loop selection with Ctrl; bridge twisting; disparate vertex counts between square and racetrack; loft convergence errors; shell/Thickness difficulties; alignment and rotation of imported geometry; probe collision; unsupported bridge areas on print. No confirmed final successful complete duct was reported.

## Starting assets

Use the existing FreeCAD document and spreadsheet-driven parameters, retaining imported STEP/STL for reference. Current work included four sketches for outer and inner profiles, separate loft attempts, and alignment issues. Keep a backup before restructuring. Distinguish **Part** Loft, **Part Design → Additive Loft**, and **Part Design → Additive Pipe**; these are different tools with different capabilities and workbench constraints.

## Workflow A: solid additive loft then Thickness

1. In **Part Design**, activate a Body. Sketch the inlet on its fan plane: closed square/rectangular perimeter dimensioned to the 5015 duct interface.
2. Create a **Datum Plane** or suitable support plane at the desired outlet location and angle. Sketch a *closed racetrack*: two parallel straight segments and two semicircular ends, constrained for symmetry, tangency, dimensions and orientation. Position provisional outlet **~6–6.5 mm lower** than the earlier hotend position, then verify against the actual FlowTech nozzle and SE probe.
3. Select inlet sketch and invoke **Part Design → Additive Loft**. Add the outlet sketch in the task dialog. Preview for a sound solid. A closed profile used in Additive Loft normally creates a solid, not just a thin surface.
4. For a controlled bend, add one or more **intermediate cross-section sketches** on datum planes between the ends, shifting and tilting them around probe features. Add them in the correct order to the loft; avoid abrupt changes and profile crossings. If a 2-profile loft fails to converge, test with simpler profiles and fewer constraints or intermediate sketches before rebuilding inner/outside passages.
5. With the resulting solid visible, **Ctrl-select the inlet and outlet planar end faces**, then invoke **Part Design → Thickness**. Start with **1.6 mm** wall, and try **Skin** and **Arc** join where available in the installed FreeCAD version. Preview that both ends are actually removed and the tube remains hollow. Thickness may fail for sharp/concave or self-intersecting offsets. A fillet may help some cases but isn't a universal cure.
6. Validate the resulting solid and STL; check wall continuity and slicing in Cura. If Thickness fails, preserve the original solid and try workflow B or a controlled subtractive inner geometry rather than repeatedly changing the original sketches blindly.

## Workflow B: multisection Additive Pipe (path-driven)

The conversation included the promising approach **Part Design → Additive Pipe**, with a sketch path and **multiple sections**. Unlike standard Loft, Pipe follows a spine/path. This is the method to investigate when the transition must bend around the SE probe.

1. Create a sketch representing the duct's guide/spine path (connected segments or a spline); ensure it starts at the inlet plane and runs through the intended route to the outlet plane.
2. Make a Body with a closed initial square section sketch positioned on the start of the path. In Part Design choose **Additive Pipe**; select the section and path using the task panel prompts.
3. Look for the feature's **Transformation / Multisection** controls and add the racetrack outlet sketch as another section, plus intermediary sections if needed. Labels and control constraints vary by FreeCAD version; verify in the actual UI before treating this as an exact click sequence.
4. Preview for section rotation, inversion, failure to converge or collisions. Use more gradual section changes and suitable intermediate path locations as needed.
5. For a solid result, test Thickness with both ends removed as above; otherwise consider an inner subtractive Pipe/Loft with measured clearances.

**Correction to previously pasted guidance:** Standard Part or Part Design Loft doesn't have the general-purpose arbitrary guide-rail selector described by some CAD tutorials. A multisection **Additive Pipe** is a more relevant FreeCAD candidate for a section change guided by a spine, but actual applicability and UI behavior must be checked against installed version and section placement.

## Other procedures and previous pitfalls

- **Parametric positioning:** Use datum-plane attachment offsets, sketches and spreadsheet expressions, not arbitrary manual transforms, so moving the outlet updates downstream geometry.
- **Separate inner/outer geometry fallback:** Existing effort used 4 sketches (outer inlet/outlet, inner inlet/outlet), attempted shell lofts and subtraction. Preserve this as a fallback, not the first approach.
- **Failure to converge:** Check sketch closedness, wire validity, coincident/duplicate edges, section order, abrupt shape changes, orientation and geometric self-intersection. A sketch appearing in the tree doesn't automatically mean it's valid as a loft section.
- **Thickness failures:** Offset can self-intersect on tight bends, narrow gaps, sharp vertices or unsuitable transition topology. Do not assume a small fillet automatically fixes it.
- **Racetrack constraints:** Tangency at line/arc junctions; concentric paired inner/outer arc centers if building manual inner geometry; consistent wall spacing; fully-constrained profiles as practical.
- **SE-specific fit:** KE remix has different probe-finger clearance; inspect correct left/right using a fixed reference view. The FlowTech tip is about 6–6.5 mm below earlier position in discussion; actual clearance must be measured.
- **Print design:** Trial 1.2 or 1.6 mm wall with 0.4 mm nozzle; verify effective slicer line width and local heat resistance, consider wall ribs and ASA reprint given PETG sagging near hotend. Keep outlets aimed at print, not heater or probe.

## Resume checklist

Inspect the latest FreeCAD Body and locate which profiles and lofts are currently valid. Try **solid Loft → Thickness** on a duplicate first. If curved avoidance is essential, compare with **multisection Additive Pipe** using a spine sketch. Record FreeCAD version, failing task-panel messages and measured outlet clearance. Do not overwrite the existing best-fit model until one method is demonstrably valid.
