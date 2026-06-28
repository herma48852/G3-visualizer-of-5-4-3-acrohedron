# G3 — non-convex 5·4·3 arcohedron

A single-file interactive 3D model of the G3 arcohedron, a non-convex polyhedron of genus 0 with 13 vertices, 24 edges, and 13 faces.

![G3](g3.html)  <!-- screenshot placeholder — open the file in a browser -->


## Quickstart

```
open g3.html
```
Or drag `g3.html` into any modern browser (Chrome, Firefox, Safari, Edge).

**No build step, no dependencies, no server required.** Three.js is fetched from a CDN at load time.


## Geometry

| Property              | Value          |
|-----------------------|----------------|
| Vertices (V)          | 13             |
| Edges (E)             | 24             |
| Faces (F)             | 13             |
| Euler characteristic  | 2 (genus 0)    |
| Symmetry              | 3-fold rotational (C₃) |

### Face types

| Face type | Count |
|-----------|-------|
| Pentagon  | 3     |
| Square    | 3     |
| Triangle  | 7     |

- 1 central triangle shares an edge with each of the 3 squares.
- 3 pentagons meet at a single 5·5·5 apex vertex **A**.
- The remaining 6 triangles form **3 dimple pairs**: each pair shares a crease edge C–D and folds inward (non-convex).

### Vertex configurations

| Config    | Count | Vertices                               |
|-----------|-------|----------------------------------------|
| 5·5·5     | 1     | A (apex)                               |
| 5·4·3     | 6     | B₁ – B₆ (pentagon ↔ square ↔ triangle) |
| 5·5·3·3   | 3     | D₁, D₂, D₃ (pentagon rim, dimple base) |
| 4·3·4·3·3 | 3     | C₁, C₂, C₃ (central triangle vertices) |

Rotational symmetry (120° about the axis through A and the centroid):

- Orbits: {A}, {C₁,C₂,C₃}, {D₁,D₂,D₃}, {B₁,B₃,B₅}, {B₂,B₄,B₆}


## Controls

| Action                | Input                        |
|-----------------------|------------------------------|
| **Orbit** (rotate)    | left-drag                   |
| **Zoom**              | scroll wheel                |
| **Pan**               | right-drag / two-finger drag |
| **Spin speed**        | slider in the top-left panel |
| **Randomize colors**  | 🎲 button                    |
| **Reset view**        | "Reset view" button          |

### Spin
The object spins about its vertical axis independently of the camera orbit. The slider controls speed (0–2 revolutions per second). Setting to 0 pauses spin while keeping full orbit control.

### Coloring
The **🎲 Randomize colors** button computes a proper face coloring (no two faces that share an edge have the same color). It uses randomized backtracking with a 4-color palette, so each press yields a different valid assignment.


## How the model is built

The HTML file contains a **fully self-contained geometry solver** that runs at load time:

1. **Symmetric seed** — vertices are placed with the correct 3-fold symmetry and the dimple tips (`B`) pulled outward relative to the crease edges (`C–D`) to bias the non-convex fold.

2. **Spring relaxation** — all 24 edges are treated as Hookean springs (rest length = 1, for unit edges). The 3 pentagons and 3 squares receive a planarity projection each iteration via Newell's method. Symmetry is re-snapped every step (averaging r, z per orbit).

3. **Scale locking** — every 25 iterations the whole structure is renormalized so the mean edge length stays at 1.

4. **Convexity verification** — the three C–D dimple edges are checked for concave dihedral (interior angle > 180°). The solver tries multiple seed parameters and picks the first that yields 3 verified dimples.

5. **Geometry upload** — faces are fan-triangulated with outward-facing flat normals into a non-indexed Three.js `BufferGeometry` with one material per face.

6. **Rendering** — `OrbitControls`, hemisphere + directional lighting, a faint edge-line overlay, and the face-coloring logic drive the interactive view.

Solver parameters and edge-length / dihedral statistics are logged to the browser console (`F12` → Console).


## Files

| File         | Description                                      |
|--------------|--------------------------------------------------|
| `g3.html`    | Complete interactive model (no external files)   |
| `README.md`  | This file                                        |


## Technical notes

- The geometry is **genus 0** (topologically a sphere) with three non-convex dimples — local indentations where two triangles share a valley-creased edge.
- The spring-solver guarantees unit-length edges within ~±5% tolerance. Pentagons and squares are planar to within the same tolerance.
- Face coloring is guaranteed proper (adjacent faces always differ). The algorithm uses backtracking with a shuffled color order at K=4, escalating to K=5+ only if needed. G3's face-adjacency graph is 4-colorable and DSATUR-backtracking typically succeeds at K=4.
