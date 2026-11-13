# Allen CCF Explorer — Quick Start Guide

A browser-based tool for exploring the Allen Mouse Brain Common Coordinate Framework (**CCFv3, 2017**) in 3D, and for planning injection and insertion targets in coordinates you can take straight to the stereotaxic frame. **Open it straight from the web (https://sjleen.github.io/allen_ccf_tools/)**, or keep the html file and the mesh data file and use it **with no installation and no internet connection**.

---

## Before you start

**Coordinate convention**

- Units are **mm**; the origin is **bregma**.
- **ML+ = right, AP+ = anterior, DV+ = dorsal.**

**Requirements**

- **Chrome · Edge**, or **Firefox · Safari from 2023 onward**. Mesh decompression relies on a built-in browser feature, so older browsers will not open the app.
- The first time you open it from the web, the **loading screen shows progress (%)** while the mesh data (about 93 MB) downloads. The browser keeps that data, so later visits open immediately.

**Why this app's coordinates are different**

The Allen CCFv3 was built from perfusion-fixed brains, so its coordinates do not line up with what you measure on the surgical table. By default this app **corrects the per-axis scaling and the overall brain angle to the flat-skull position**, so the coordinates you read on screen are close to real stereotaxic coordinates.

> To switch the correction off and see raw CCF coordinates, set **Preset** in the **Coordinates** tab to **Raw CCF**; to tune the values yourself, choose **Custom**.

---

## Layout

| Area | Contents |
| --- | --- |
| **Left panel** | **Ontology** (structure list) · **Mesh Source** · **Coordinates** (coordinate correction) tabs |
| **Center viewport** | **3D view** on top, **Coronal · Sagittal · Horizontal** 2D section views below |
| **Right panel** | **Points** (target registration) · **3D View** (display options) · **Export images** (image output) |

Both side panels collapse via the buttons at the **top-left / top-right of the viewport** — handy on a small screen.

---

## 1. First, once: match bregma to your own

**If you are starting fresh, do this before anything else.** The bregma each experimenter works from differs slightly, so calibrating once improves the accuracy of every coordinate afterwards.

Adjust it in the **Coordinates** tab → **Bregma / Lambda personalization** section. **The Bregma AP / DV boxes start empty, and points cannot be created or imported until they are filled in** (enter 0 for no correction). A recommended procedure:

1. Pick a target that is narrow along the AP axis. The **anterior commissure** (ML = midline ± 1 mm) works well.
2. Inject a small amount of dye — for example **2–5 nL** of Trypan Blue.
3. Register the coordinates you used as a point. (See section 3 for how to register points.)
4. Adjust **Bregma AP** and **Bregma DV** until the registered point sits where histology shows the injection.
5. Record the final values, or save the current session and reuse it as a **personal profile**. (See section 5 for saving sessions.)

The bregma–lambda distance (**Bregma to lambda**, 4.2 mm by default) is adjustable in the same section.

---

## 2. Working with the views

**Mouse controls**

| | Rotate / pan | Zoom |
| --- | --- | --- |
| **3D view** | left-drag = rotate, right-drag = pan | wheel |
| **2D view** | left-drag = pan | wheel |

**Moving through sections**

Use the slider below each 2D view to move the section plane. You can **drag** it, roll the **wheel** over it, or **type a value** into the number box.
Holding **Ctrl while rolling the wheel** changes the step from 0.1 to 0.01 mm — **10× finer**.

**Enlarging a single view**

The **⛶** button at the top-left of each 2D view (bottom-right for the 3D view) makes that view fill the whole center area. While maximized, press **⤡** or **Esc** to return to the four-view layout.

**Showing and hiding structures**

Toggle regions in the **Ontology** panel on the left, or find them with the search box by name, acronym or ID.

**Click the color square** on the left of any entry to change its color. The new color is applied to the 3D mesh, the 2D sections, the list and the path-analysis plot at once, and structures you have not switched on yet can be set in advance.

> **The color picker** — pick from the square and the hue strip, or type straight into the **HEX · HSB · RGB** boxes (the HEX box also takes `rgb()`, `hsl()` and CSS color names). The **eyedropper** samples any color on your screen, **Reset to original** restores the color it started with and **Reset all** does the same for every entry. **Esc** cancels and restores the color you opened with.

> **Dimmed entries** are structures with no mesh in the Allen CCFv3. Of the roughly 1,300 structures listed, only about 840 come with meshes, so subdivisions below a certain level cannot be displayed and their toggle is disabled. This is a limit of the source dataset, not of the app.

---

## 3. Registering points

Register your targets in the **Points** panel on the right.

- Click **New point** to create one, then set its name, coordinates, and **radius**. The radius is there to gauge the spread during infusion.
- **Take coordinates by clicking a 2D view** — press the **◎ PICK** button at the right end of the coordinate row, and the cursor becomes a crosshair over the 2D views. Click the spot you want and its coordinates are filled in automatically. Press **Esc** to cancel.
- **Change its color** — click the color square on the left of the row to open the same picker described in section 2.
- **Double-click** a point to move the 2D views to it. Tick **Mirror** to also draw the same point and path in the other hemisphere (ML sign flipped).
- For many coordinates at once, use **Import points (CSV)**. Register one point and run **Export points (CSV)** to see the exact format the importer expects (mirror points are exported as extra rows with `(mirror)` after the name).

---

## 4. Path analysis: checking the insertion route

Tick **Path** on a point to draw the route that reaches it and create a **Path analysis** entry. Open that entry to see **every region the route passes through**.

- The window **updates in real time** as you change coordinates or angles.
- Enter **Tilt** and **Azimuth** to simulate an angled approach. The **manip value** shown tells you where to set the manipulator to reach that point, assuming bregma is zeroed at ML = AP = DV = 0.
- The buttons at the top of the **Path analysis** window export it as **SVG** (for figures) or **CSV** (for analysis).

> **Matching your manipulator** — in the **Coordinates** tab, the **Manipulator convention** section lets you set the ML, AP, DV axis directions and the **order in which tilt · azimuth are applied** to match the physical build of your hardware. The defaults follow the common stereotaxic arm.

> **Reading the plot** — the horizontal direction is the **ontology level** (broader structures on the left, finer on the right, with the level number along the top). Because of how the CCF meshes are defined, one spot can belong to two or more regions at once; when several regions at the same level share a stretch, they split that level's width — half each for two, a third each for three. A stretch with no overlap uses the full width.

---

## 5. Saving and sharing

**Save session**, next to the app name at the top-left, writes the current settings, your colors and the registered points to a `.json` file. **Load session** reads it back.

- Type a note in the box just above **Save session** and it is appended to the filename after an underscore (e.g. `ccf-session-…_mouse42.json`).
- Hand that `.json` (or the app's html file) to someone else running the same app and they can **reproduce your exact state**.

---

## 6. Exporting the viewport as an image

The **Export images** panel on the right holds three buttons.

| Button | Result |
| --- | --- |
| **3D → PNG** | the 3D view as a high-resolution PNG |
| **2D → PNG** | the current 2D sections as a PNG |
| **2D → SVG** | the current 2D sections as **vector** artwork |

SVG lets you restyle strokes and colors freely in Illustrator · Inkscape, which **suits figure preparation for papers**.
The 2D exports follow what is on screen — if one view is **maximized you get that view alone**, otherwise **all three sections side by side**.

---

## Also worth knowing

- **Mesh opacity** (**3D View** panel) — adjusts how transparent the meshes are.
- The bottom-left corner of the viewport continuously shows the **coordinates** and **region name** under the cursor.
