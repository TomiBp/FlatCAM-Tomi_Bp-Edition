# Tomi Bp Edition — feature notes

This document describes the implementation present in this source snapshot. It is not a substitute for testing a specific PCB, tool, material or CNC machine.

## 1. EAGLE 10× Excellon coordinate correction

**Location:** Excellon Object → Transformations.

The checkbox is connected to the Excellon object's scaling logic in `appObjects/FlatCAMExcellon.py`.

- When enabled, the object applies a scale factor of `0.1` to Excellon drill and slot coordinates.
- The implementation comment and code indicate that tool diameters are not scaled.
- When disabled after the correction has been applied, the object applies the inverse factor `10.0`.
- The checkbox is currently initialized as checked (`set_value(True)`), so newly initialized Excellon objects apply the correction by default.
- The EAGLE icon is loaded from `assets/resources/eagle_monochrome32.png`.

**Important:** the default-on behavior can change imported geometry immediately. Verify the resulting dimensions and coordinates before generating CNC code. Do not assume every EAGLE export uses the same coordinate convention.

## 2. NCC board perimeter

**Location:** NCC Tool parameters.

The `Board perimeter` checkbox is enabled by default in the current source.

The implementation:
- selects the NCC reference object (or selected reference object according to the active NCC selection mode);
- derives an outer convex hull from its solid geometry;
- offsets that hull outward by half the diameter of the largest NCC tool that actually cleared copper;
- appends the resulting exterior ring geometry to that tool's geometry.

The generated perimeter is therefore based on a **convex hull**, not a traced outline of every concavity in the PCB edge. It can bridge inward notches and concave sections. Review the generated geometry carefully. The feature is not a replacement for a dedicated board-outline/cutout workflow when an exact PCB profile is required.

The code includes handling for polygon and multipolygon exterior rings and adjusts ring direction according to the selected milling type.

## 3. NCC tool-to-tool overlap profiles

The NCC tool stores overlap profiles in application defaults under:

`tools_ncc_tool_overlap_profiles`

Profiles are keyed by formatted tool diameter. The explicit **Save tool-to-tool overlap** button stores the currently selected tool's overlap value for that diameter. The values are written through the application's defaults persistence mechanism.

This is a convenience profile mechanism; confirm the selected tool and displayed overlap before saving or generating geometry.

## 4. UI and resources

- The NCC UI includes the board-perimeter checkbox and overlap-save button.
- The Excellon transformations area includes the EAGLE checkbox, monochrome icon and descriptive label.
- The EAGLE icon used at runtime is stored in `assets/resources/`.

## Known limitations / items for validation

- No automated geometric regression suite for the EAGLE scaling or NCC perimeter feature is included in this snapshot.
- NCC perimeter generation uses a convex hull, so it does not preserve concave board outlines.
- GUI appearance and behavior have been visually checked in the maintainer's FlatCAM 8.994 / Windows environment, but not across all Qt styles, display scaling settings or operating systems.
- A full end-to-end CNC test on a physical machine is outside the scope of repository static checks.


## Upstream provenance

This edition is a derivative of FlatCAM BETA 8.994. The upstream BETA source repository maintained by Marius Stanciu is:

- https://bitbucket.org/marius_stanciu/flatcam_beta

The original FlatCAM repository by Juan Pablo Caram is:

- https://bitbucket.org/jpcgt/flatcam

The official project website links to the jpcgt repository for development sources: https://www.flatcam.org/download

This project is an independent personal/community edition. It is not the official upstream repository and is not endorsed by the upstream maintainers.
