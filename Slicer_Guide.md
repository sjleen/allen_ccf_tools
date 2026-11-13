# Allen CCF Slicer

A fork of [Allen CCF Explorer](https://sjleen.github.io/allen_ccf_tools/explorer.html) that cuts the coronal section at an angle, so you can find the tilt of your own histology sections and step through the series at the real section thickness.

Open it on the web at https://sjleen.github.io/allen_ccf_tools/slicer.html, or keep the html and `ccf_mesh_data.js` together and open the html locally.

Explorer's 3D view, ontology tree, flat-skull calibration, exports and sessions work the same. **Two inputs start empty on purpose and must be filled in first:** the bregma AP / DV shift (Coordinates tab; enter 0 for no correction — tracks cannot be added or imported until it is set) and the section **Thickness** (Slicing tab — section numbers, stepping, Series → CSV and the track fit stay off until it is set). This fork adds the **Slicing** tab (left) and **Track fit** (right panel, in place of Explorer's Points, which are hidden here together with the Path filter tab), and makes the Coronal view follow the tilted plane.

## Why it exists

Brains are never blocked perfectly square to the coronal plane. A small pitch (dorsal edge further forward than ventral) or yaw (one hemisphere further forward than the other) makes every section look "off" against a standard coronal atlas. This app finds those two angles and shows the atlas cut the way your sections were actually cut.

## How coordinates are computed (two steps, every time)

1. **CCF → flat skull** (Coordinates tab) — Explorer's own correction: per-axis scale about bregma, then the whole-brain tilt. Gives stereotaxic ML / AP / DV from bregma.
2. **Flat skull → cutting plane** (Slicing tab) — inside that frame, the section plane is `AP = A + tan(yaw)·ML + tan(pitch)·(DV − DVref)`. Section *k* is a slab of the given thickness; neighbouring sections are exactly one thickness apart, perpendicular to the plane. Sections are numbered from **#0** with the same sign as AP: **+ toward anterior, − toward posterior** (*#0 center AP* sets where #0 is).

Only the Coronal view is tilted. Sagittal and Horizontal stay axis-aligned and mark the current section (orange) and its neighbours; the 3D view shows the plane as an orange sheet.

## Finding the angle

**Manual** — turn pitch and yaw in the Slicing tab while comparing the Coronal view with your sections.

**From tracks** — tracks are entered in a table (one row per track, right panel). A new track starts with empty coordinates; a track counts only once its ML / AP / DV are filled in. For each injection or probe track enter:

- the coordinates you used (flat skull, ML / AP / DV, optional angles);
- *Span* — how many sections the track appears in, from the section with the brain surface to the section with the tip, both counted (1 = the whole track sits in one section);
- *Deeper goes* — whether the track moves into more posterior or more anterior sections as it goes deeper;
- *Tip offset* (optional) — where this tip sits relative to the other tips, in sections, signed like AP: **+ = more anterior, − = more posterior** (−4 = four sections posterior). Pick one tip as 0 -- an empty offset on the first track counts as 0. Once the plane follows the fit, the tip at 0 sits in section #0, so section numbers read exactly like offsets (a track with span 11 that goes posterior as it deepens runs from #+10 at the surface to #0 at the tip).

The last two columns show the pitch and yaw that this one track implies on its own (pitch from its span and direction, yaw from its tip offset against the reference tip; tracks at nearly the same ML as the reference show “—”), so a track that disagrees with the rest stands out. Span and direction alone give the **pitch**. Tip offsets on tracks at different ML add the **yaw** (and place section #0); without them those stay at their current values. All tracks go into one least-squares fit. The result table puts what you entered next to what the fitted plane predicts for every track; a row off by more than one section is highlighted.

**The plane follows the fit automatically** once every track has ML / AP / DV and a span: pitch, yaw and the #0 position are copied into the Slicing controls, and the view stays on the section number you were looking at. Moving an angle, *Reset angles* or *#0 center AP* by hand switches to **manual** (the plane stops following); **Follow fit** turns following back on and jumps to the section that holds the first track's tip. The mode is saved with the session (sessions from before this change open in manual).

**Also fit effective section thickness** treats the real thickness per section as one more unknown, for when shrinkage or compression makes it differ from the vibratome setting. It needs tracks seen at different section numbers (tip offsets on two or more tracks, or spans), and the fitted value replaces Thickness when the plane follows the fit. With only one or two tracks leave it off — the thickness can soak up angle error.

**Zeroing error** (ML / AP / DV, above the table) shifts every track at once, for when bregma or the midline was set slightly off at surgery: enter 0.1 under AP instead of editing every track. Tracks are drawn and fitted at entered + shift; your entered values and the track CSV stay unchanged, and the shift is saved with the session. Signs are stereotaxic (+ right / anterior / dorsal); DV is ignored when Target DV is depth from the brain surface.

**Import CSV** also reads a point list exported from Explorer (`label,ml,ap,dv,color`): labels become track names, colors are kept, and Target DV switches to "from bregma". Empty ML / AP / DV cells stay empty. Explorer's CSV carries no angles, so set them by hand for angled tracks.

Resolution follows from counting whole sections: with a 3 mm track and 65 µm sections, one section of span is about 1.2° of pitch, so more tracks give a better average.

In the Coronal view each track is drawn dashed (projected), with the part that really lies inside the current section drawn solid; the surface and tip points appear as white circles on their sections when a tip number is given. In the 3D view each track is a rod from its brain-surface entry to the tip, with a ball at the tip, in the track's color.

## Browsing the series

- ◀ ▶ in the Slicing tab or under the Coronal view, or ← → (Shift = ×5).
- *Jump to AP* goes to the nearest section; **Series → CSV** lists every section with its center AP and the AP at the dorsal, ventral, left and right edges.
- The Coronal slider still moves freely; the header shows `#k` on a section and `≈#k` in between.

## Files

- `allen_ccf_slicer_v1_0.html` — the app (Explorer v1.0.1 + slicing).
- `ccf_mesh_data.js` — the same mesh data file Explorer uses; keep it next to the HTML.

Sessions are saved as `ccf-slicer-session-<time>_<note>.json` (images as `ccf-slicer_*.png/svg`) so they are not confused with Explorer files. They use Explorer's format with an extra `slicer` block, so Explorer sessions open here too.

## Notes and limitations

- The fit is only as good as the stereotaxic placement of the tracks; an off-target track shows up as a large residual.
- Tracks are not drawn in the 2D SVG export (PNG exports include them).
- Tissue shrinkage is not modeled, except through the optional fitted thickness.

## Data source and citation

Atlas data: **Allen Institute** — Allen Mouse Brain Common Coordinate Framework (CCFv3, 2017), [portal.brain-map.org](https://portal.brain-map.org), used under the [Allen Institute Terms of Use](https://alleninstitute.org/legal/terms-use/). Please cite according to their [citation policy](https://alleninstitute.org/legal/citation-policy). 3D rendering uses [three.js](https://threejs.org) (MIT).

---

Built by [sjleen](https://github.com/sjleen).
